# Transfer Files Command Line

There are many command-line tools for transferring data to and from SIUE systems, each with intended uses and specific feature sets. The following table lists the available command-line tools based on the transfer scenario:

| Scenario | Options |
| --- | --- |
| Local computer ⇄ SIUE systems	| sftp, scp, rsync, globus-cli |
| SIUE systems ⇄ Internet	|   File servers: sftp, lftp<br>Downloads: wget, curl, aria2c<br>Cloud storage: rclone<br>Code: git<br>

Below, you will find descriptions, comparisons, and examples of how to use each tool.

If you have questions about transferring data using these tools, please contact its-cluster-support@siue.edu and we will assist you.

### General recommendations

- Only transfer data that is necessary
- Compress large files using `xz` to reduce size of transfer (depending on network speed)
- Archive files using `tar` when transferring large numbers of files
- For small-to-medium transfers to/from your local computer, use `sftp` or `rsync`
- For large transfers to/from your local computer or other endpoint, use Globus
- For backing up and syncing directories, use `rsync`
- For transfers to/from an FTP server, use `lftp`
- For faster internet downloads, use `aria2c`
- For transfers to/from cloud storage, use `rclone`

### Archiving and compressing before transferring

Creating and compressing a single archive file can be useful before transferring files to or from SIUE systems, especially for directories with a large number of files (e.g., > 1000, regardless of the total size of those files). Each file has associated metadata, and the transfer can be slowed by attending to that metadata. Compressing files will reduce the amount of data that needs to be transferred. However, it takes time to compress and uncompress files, so the total transfer time may not necessarily decrease depending on factors like network speeds. With fast network speeds, relative to total transfer size, it is typically not worth compressing files. For more information, see the section on archiving and compressing files in the guide for [Managing Files Using the Command Line](user_guides/data_management/manage_files_command_line?id=archiving-and-compressing-files).

### Local computer ⇄ SIUE systems

To copy files between your local computer and SIUE systems, the available options are `sftp`, `scp`, and `rsync`. These are available on macOS and Linux through the native terminal applications and on Windows through applications like Windows Terminal or [PuTTY](https://www.putty.org/). Globus also provides a command-line interface (`globus-cli`) that you can install; for more information, see the guide for [Transferring Files Using Globus](user_guides/data_management/transfer_files_globus.md).

`sftp` provides an interactive mode that requires authenticating only once and maintains an open connection to transfer files as needed until the session is exited. In contrast, `scp` and `rsync` can only be used in a non-interactive mode that requires authentication for each transfer. In addition, `rsync` has more advanced features than `scp`.

Instructions and commands for these tools are detailed in the collapsible sections below:

<details>
  <summary>
    <b>sftp</b>
  </summary>

#### Using sftp

`sftp` is a client program for transferring files using the Secure File Transfer Protocol (SFTP). It can be used in interactive or non-interactive modes to copy files between two computers over a network, one local and one remote. In interactive mode, it requires an initial login and authentication, but your session will remain open until you exit or are otherwise disconnected. You will remain connected to SIUE systems with the ability to upload (`put`) and download (`get`) files without further authentication. This is a benefit of using `sftp` compared to the other command-line transfer tools.

To use `sftp` in interactive mode, from your local computer, first log in to a SIUE node like dtn.hpc.siue.edu authenticate via e-ID password:

```
sftp miwalls@dtn.hpc.siue.edu
```

If it is your first time logging in, you will be asked "Are you sure you want to continue connecting (yes/no)?". Enter "yes". You will see the following once you are connected:

```
Connected to dtn.hpc.siue.edu.
sftp>
```

Enter the `help` command to view all the available commands. Use commands like `pwd`, `ls`, and `cd`, and their local equivalents `lpwd`, `lls`, and `lcd`, to navigate to the source and destination directories for file transfers.

#### Navigating locally

```
sftp> lpwd
Local working directory: /home/miwalls
sftp> lcd myimages
sftp> lls
myplot1.jpg myplot2.jpg
```

#### Navigating remotely
```
sftp> pwd
Remote working directory: /home/miwalls
sftp> cd /scratch/miwalls/images
```

#### Uploading file/directory from local computer to SIUE systems

To upload a file, use the `put`command:

```
sftp> put myplot1.jpg myplot.jpg
Uploading myplot1.jpg to /scratch/miwalls/myplot.jpg
myplot1.jpg                                 100%   10KB   2.4MB/s   00:01    
```

To upload a directory, recursively, add the `-R` option and specify the path to the local directory (e.g., `put -R dir`).

#### Downloading file/directory from SIUE systems to local computer

To download a file, use the `get`command:

```
sftp> get myplot3.jpg myplot3.jpg
Fetching /scratch/miwalls/myplot3.jpg to myplot3.jpg
/scratch/miwalls/myplot3.jpg                100%   10KB   2.4MB/s   00:01    
```

To download a directory, recursively, add the `-R` option and specify the path to the remote directory (e.g., `get -R dir`).

</details>

<details>
  <summary>
    <b>scp</b>
  </summary>

#### Using scp

`scp`is a client program for transferring files using the Secure Copy Protocol (SCP). It copies files between two computers over a network, one local and one remote.

?> Note: Unlike `sftp`, login and authentication are requested for each use of the scpcommand.

A generic `scp` command is:

```
scp <options> source destination
```

where `source` and `destination` are file or directory paths and one of these is on a remote host where the syntax becomes `host:path`. With SIUE systems, the host is a login or transfer node. When the command is submitted, you will first need to enter your password and complete the Duo authentication, and then the transfer will begin.

To **upload** a local file to your project directory, for example, the destination is on a remote host. From your local computer, enter a command like the following:

```
scp /home/tommy/data.csv miwalls@dtn.hpc.siue.edu:/project/miwalls
```

To upload a directory, recursively, add the `-r` option and specify a local directory as the source.

To **download** a file from your project directory, for example, the source is on a remote host. From your local computer, enter a command like the following:

```
scp miwalls@dtn.hpc.siue.edu:/project/miwalls/data.csv /home/miwalls
```

To download a directory, recursively, add the `-r` option and specify a directory on the host as the source.

#### scp options

For large transfers, consider adding the `-C` option, which will compress the source files before transferring and automatically uncompress them after they are copied to the destination.

Enter `man scp` for more information and to view all available options.

</details>

<details>
  <summary>
    <b>rsync</b>
  </summary>

#### Using rsync
[Rsync](https://rsync.samba.org/) is a fast and versatile transfer tool for synchronizing files and directories. It is typically used to copy, sync, and back up directories between two computers over a network, one local and one remote, but it can also be used for local copying and syncing. It uses a delta-transfer algorithm to minimize the amount of data that needs to be transferred; only new or modified files in a directory will be transferred. By default, Rsync will use SSH to securely transfer files over network.

?> Note: Unlike `sftp`, login and authentication are requested for each use of the `rsync` command.

A generic `rsync` command is:

```
rsync <options> source destination
```

where `source` and `destination` are file or directory paths and one of these is on a remote host where the syntax becomes `host:path`. With SIUE systems, the host is a login or transfer node. When the command is submitted, you will first need to enter your e-ID password, then the transfer will begin.

To **upload** a local directory to your project directory, for example, the destination is on a remote host. From your local computer, enter a command like the following:

```
rsync -avh /home/miwalls/data miwalls@dtn.hpc.siue.edu.edu:/project/miwalls
```

To **download** a directory from your project directory, for example, the source is on a remote host. From your local computer, enter a command like the following:

```
rsync -avh miwalls@dtn.hpc.siue.edu:/project/miwalls/data /home/miwalls
```

The `-a` option enables archive mode, which recursively transfers directories and preserves permissions and modification times for files. The `-v` option enables verbose mode, which prints a log of the transfer. The `-h` option prints transfer size and related information in a human-readable format.

After making changes to a source directory, simply enter the same `rsync` command again to sync the destination directory. If files deleted from the source should also be deleted from the destination, add the `--del` option.

Please note that the `rsync` command is sensitive to a trailing `/` on the source directory (e.g., data vs data/). If not included, it will copy the directory as well as its contents to the destination directory as a new subdirectory. If included, it will not copy the directory itself but only the contents to the destination directory.

#### rsync options
Rsync provides many other options than those used in the examples above. Here are some other useful options:

| Option | Description |
| --- | --- |
| --del	| Delete files from destination if deleted from source |
| -z or --compress| Compress files during transfer |
| --append-verify | Keep, check, and update partially transferred files |
| --progress | Display progress of file transfers |
| --stats	| Print transfer statistics |

For transfers of large files that may take a long time, consider adding the `-z` option to compress files as well as the `--append-verify` option, which will keep partially transferred files. If the transfer is interrupted, re-entering the same command will restart the transfer where it stopped and append data to the partial file.

Enter `man rsync` or `rsync --help` for more more information and to view all available options.

Note: If you experience issues with disconnections during an `rsync` transfer, try adding the option `--timeout=60` to keep the connection alive for 60 seconds in case the transfer idles. Sometimes network latency can cause disconnects.

</details>

### SIUE systems ⇄ Internet

There are many tools available to transfer files to and from SIUE systems and endpoints on the public internet, such as FTP file servers or HTTP web servers.

<details>
  <summary>
    <b>File servers: sftp and lftp</b>
  </summary>

#### Using sftp and lftp

For file servers that use the SFTP protocol, you can use the `sftp` program to transfer files. Examples of how to use `sftp` can be found in the previous section on `sftp` above, with the only difference being the remote server that you interact with.

For file servers that use FTP, SFTP, or other FTP-like protocols, you can use the `lftp` module to transfer files. The `lftp` program has a similar interface and commands to `sftp` but has additional features, including multi-connection and parallel downloads. For more information and available options, enter `man lftp` or see the official [lftp documentation](https://lftp.yar.ru/).

The `wget`, `curl`, and `aria2c` programs can also be used to non-interactively download files from FTP or SFTP servers. The `sftp`, `lftp`, and `curl` programs can also be used to non-interactively upload files to FTP or SFTP servers.

</details>

<details>
  <summary>
    <b>Downloads: wget, curl, and aria2c</b>
  </summary>

#### Using wget, curl, and aria2c

The main tools focused on downloading files from the web (i.e., from sources using HTTP and HTTPS protocols, like web sites) are `wget`, `curl`, and `aria2c`. They can also be used to non-interactively download files from FTP or SFTP servers.

In general, `wget` is the simplest to use, `curl` offers more advanced features useful in scripting, and `aria2c` offers multi-connection and parallel downloads to improve the speed of large transfers.

#### Using wget
For simple file downloads from the web, the `wget` program is the easiest to use. Just provide the URL to the file:

```
wget <url>
```

Enter `man wget` or `wget --help` for more information and to view all available options.

#### Using curl

The `curl` program supports more protocols and provides more advanced features for downloading (and uploading) files, especially for scripting purposes. For a simple file download, use the `-O` option and provide the URL to the file:

```
curl -O <url>
```

Without the `-O` option, `curl` will simply print the contents to the screen. This is the default behavior and is useful when piping the contents of a file as input into another command.

Enter `man curl` or `curl --help` for more information and to view all available options. For even more information, see the official [curl documentation](https://curl.se/).

#### Using aria2c

For large downloads, the `aria2c` program is a better choice because it offers multi-connection and parallel downloads that can reduce transfer times. For a simple file download, just provide the URL to the file:

```
aria2c <url>
```

For a large file, add the `-x` option to use multiple connections to the file that will reduce the download time. For example, the following command opens 4 connections:

```
aria2c -x4 <url>
```

You can also specify a list of URLs in a file using the `-i` option and then use the `-j` option to specify the number of files to download in parallel. For example, given a file urls.txt that lists a URL to a file on each line, the following command will download 4 of these files concurrently:

```
aria2c -i urls.txt -j4
```

Enter `man aria2c` or `aria2c --help` for more information and to view all available options. For even more information, see the official [aria2 documentation](https://aria2.github.io/).

</details>

<details>
  <summary>
    <b>Cloud storage: rclone</b>
  </summary>

#### Using rclone

For cloud storage, you can use the `rclone` module to transfer files. This requires some initial setup and configuration. For more information, see the guide for [Transferring Files Using Rclone](user_guides/data_management/transfer_files_rclone.md).

</details>

<details>
  <summary>
    <b>Code: git</b>
  </summary>

#### Using git

[Git](https://git-scm.com/) is a source-code management program useful for version control and collaborative development. You can use `git` commands to manage code repositories and push and pull changes to and from CARC systems. We recommend using a central remote repository at services like GitHub, GitLab, or BitBucket. You can develop code directly on CARC systems in a Git repository in one of your directories and use the remote repository to back up and sync changes. You can also develop code on your local computer as part of a Git repository, push changes to a remote repository, log in to SIUE systems, and pull the changes to the corresponding repository located in one of your directories.

Enter `man git` or `git --help` for more information. For even more information, see the official [Git documentation](https://git-scm.com/doc).

</details>

### Verifying file integrity after transfers

Regardless of which command-line transfer tool you use, you may wish to ensure file integrity after file transfers. Some of the tools described above have built-in options to verify file integrity — check the tool's documentation to confirm this and learn how to use the option. Alternatively, you can use SHA-256 checksums, for example, to verify that files were successfully copied.

To generate checksums at the source directory, the exact command to use will differ depending on the system. On Linux, the command is `sha256sum`; on macOS, the command is `shasum`; and on Windows, the command is `Get-FileHash`. You may also be able to use GUI apps to generate checksums as well. Using Linux as an example, in the source directory enter a command similar to the following:

```
find . -type f -exec sha256sum '{}' \; > sha256sum.txt
```

This will generate the file `sha256sum.txt`. Copy this file to the destination directory where files were transferred, and then from that directory enter:

```
sha256sum -c sha256sum.txt
```

This compares the file checksums from the source with the file checksums in the destination and prints the results. The transfer was successful if all of the checksums match, as indicated by an `OK` status. Note that the `sha256sum.txt` file itself will fail because it was not originally present in the source directory.
