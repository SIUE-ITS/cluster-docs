# Managing File Using the Command Line
The following sections describe how to use command-line tools to manage files on SIUE systems.

### Organizing files
Project files should be organized within a directory structure of some kind in order to keep files organized, documented, and findable. This may include, for example, having separate directories for raw data, processed data, and code.

To list files and directories, use the `ls` command. For example, to list files in long format for the current directory use:

```
ls -l
```

For other directories, add the directory path to the command. Enter `man ls` or `ls --help` for more information and to view all available options.

To create a directory, use the `mkdir` command:

```
mkdir directory_name
```

Enter `man mkdir` or `mkdir --help` for more information and to view all available options.

To copy files or directories, use the `cp` command:

```
cp /source/path /destination/path
```

For example, to copy a directory on /scratch to /project, use:

```
cp -r /scratch/cougar/dir /project/cougar_1/
```

The `-r` option, recursive mode, is needed when copying directories. To print a log of the copying, add the `-v` option, which enables verbose mode. To copy multiple files or directories to the same destination, simply include additional source paths in the command. Enter man `cp` or `cp --help` for more information and to view all available options.

Note: Do not use the `-a` or `-p` options if you are copying locally into a project directory, because this could result in incorrect file permissions.

To move files or directories (i.e., copy and also remove the files from the source), use the `mv` command instead:

```
mv /source/path /destination/path
```

To rename files, you can also use the `mv` command:

```
mv /source/filename.txt /source/newfilename.txt
```

If you are backing up and syncing a directory, use an `rsync` command. For example:

```
rsync /source/dir/ /destination/dir/
```

Rsync will copy only files that are new or have changed in the source directory. Enter `man rsync` or `rsync --help` for more information and to view all available options.

Note: Do not use the `-a` or `-p` options with `rsync` if you are copying locally into a project directory, because this could result in incorrect file permissions.

To delete files or directories, use the `rm` command:

```
rm /path/to/file
```

For example, to delete a directory, use:

```
rm -r /scratch/cougar/dir
```

The `-r` option, recursive mode, is needed to remove directories. To remove multiple files or directories, simply add additional paths to the command. Enter `man rm` or `rm --help` for more information and to view all available options.

### Checking file disk usage

To check the disk usage of files and directories, use the `du -h` command:

```
du -h /path/to/file
```

?> Please note that all file systems run CephFS which compresses files, so the file size on disk may be smaller than the actual file size (on your local computer, for example). Using the `du --apparent-size -h` command will give the uncompressed file size, and the `ls -lh` command should give the same result.

To list the ten largest files or subdirectories in the current directory, enter:

```
du -s * | sort -nr | head -n 10
```

`du -s *`: Summarizes disk usage of all files
`sort -nr`: Sorts numerically, in reverse order
`head -n 10`: Shows the first ten lines from head
Enter `man du` or `du --help` for more information and to view all available options.

### Sharing files
The `/project` directories are the best place to share files. By default, the members of a project group will have full read, write, and execute permissions for all files in a project directory (i.e., permissions set to 2770 = drwxrws---).

You can check the current permissions for a file or directory with the commands `ls -l /path/to/file` or `getfacl /path/to/file`.

> When sharing your files, please keep the following in mind:
> - Never set the permissions of your directories to 777 (drwxrwxrwx), which means that anybody can access and delete your files.
> - Do not share or change the permissions of your /home directory and its subdirectories. If something goes wrong, you may be blocked from logging in because SSH requires strict permissions for logging in.
> - Granting other users read permission for your files (r--) and read and execute permissions (r-x) for your directories is typically sufficient for sharing. Granting write permission can result in modified or deleted files, so only provide write permission when actually needed.

You can change file and directory permissions using `chmod` or `setfacl` commands.

For example, to provide read and execute permissions `(r-x)` but not write permission to a project subdirectory for your project group, use:

```
chmod 750 /project/cougar_1/dir
```

If the subdirectory is actually located within another subdirectory, note that the group would also need read and execute permission to the full hierarchy of subdirectories. Granting write permission to a directory allows users to create, modify, or delete files in that directory, also depending on individual file permissions. Enter `man chmod` or `chmod --help` for more information and to view all available options.

For fine-grained access control, use a `setfacl` command instead, which will create access control lists (ACLs). For example, to provide read permission `(r--)` to a specific file for a specific user, enter:

```
setfacl -m u:<username>:r-- /path/to/file
```

To remove access, use the `-x` option to remove all permissions for that user:

```
setfacl -x u:<username> /path/to/file
```

Other useful options include `-R` to set permissions recursively and `-d` to set default permissions for new files. Enter `setfacl --help` to view all available options, and for more information view the manual page [here](https://man7.org/linux/man-pages/man1/setfacl.1.html).

Use the `getfacl` command to view the currently set ACL permissions for files and directories:

```
cougar@login.hpc.siue.edu:~$ getfacl /project/dir
getfacl: Removing leading '/' from absolute path names
# file: project/dir
# owner: root
# group: dir
user::rwx
user:guest_user:r-x
group::---
mask::r-x
other::---
default:user::rwx
default:user:guest_user:r-x
default:group::---
default:mask::r-x
default:other::---
```

### Backing up files
The `/home`, `/project`, and `/bulk` file systems do not file recovery capabilities, we encourage you to back up your files elsewhere. There are a few different backup locations to consider:

- Local storage (e.g., external drive)
- Cloud storage
- Research data repositories

To transfer files to local or cloud storage, see our guide for [Transferring Files Using the Command Line](user_guides/data_management/transfer_files_command_line.md). Rsync is especially useful for syncing to a backup directory on local storage, and [Rclone](user_guides/data_management/transfer_files_rclone.md) works similarly for cloud storage. For large transfers to local or cloud storage, [Globus](user_guides/data_management/transfer_files_globus.md) can sync two directories in a similar manner.

Research data repositories, such as [OSF](https://osf.io/), [Zenodo](https://zenodo.org/), [Harvard Dataverse](https://dataverse.harvard.edu/), and [Dryad](https://datadryad.org/stash), are a special type of cloud storage intended for sharing research data with the wider research community. These services typically have an API that can be used at the command line to upload files directly from SIUE systems.

As part of the process of backing up files, you can also create a single archive file containing multiple files and directories using `tar` (see section below). This may be useful for versioning and organizing backups.

### Archiving and compressing files
Archiving and compressing files can help simplify file organization and save storage space, such as after a project is completed and the associated files are not needed in the immediate future. This is also useful for packaging project files in order to distribute them to other researchers, for example. You can use a combination of the programs `tar` for archiving files and `gzip` or `xz` for compressing files.

#### Archiving with tar
To create an archive file from a directory of files, use the `tar` command. For example:

```
tar -cvf <filename>.tar <dir>
```

To add multiple directories and files, simply add the paths to these directories and files in the command. To check the integrity of the files, add the `-W` option.

To extract the archive, use the `-x` option instead of the -`c` option. For example:

```
tar -xvf <filename>.tar
```

Note that the .tar file will be larger in size than the sum of all the files being archived, primarily because of the added file headers in the archive file. Enter `man tar` or `tar --help` for more information and to view all available options.

#### Compressing with gzip
To compress files using `gzip`, use:

```
gzip -v <filename>
```

This will create a .gz file. Including the `-v` option, verbose mode, will print the compression ratio. There are 9 levels of compression, with 9 being the highest/slowest level and 6 being the default. The default is typically the best value to use with respect to the compression/time tradeoff. To maximize compression, at the expense of compression time, add the `-9` option.

To uncompress a .gz file, add the `-d` option:

```
gzip -dv <filename>.gz
```

Enter `gzip --help` to view all available options. In addition, the `pigz` module is a parallel implementation of `gzip` that provides faster compression and uncompression times. It can be used as a drop-in replacement for `gzip` commands.

#### Compressing with xz
For better compression ratios or for maximum compression, use `xz` instead of `gzip`. With `xz` you can also use multiple cores to speed up the compression time. For example, to compress using 4 cores, add the `-T4` option:

```
xz -v -T4 <filename>
```

This will create a .xz file. Including the `-v` option, verbose mode, will print compression progress and related information. There are 9 levels of compression, with 9 being the highest/slowest level and 6 being the default. The default is typically the best value to use with respect to the compression/time tradeoff. To maximize compression, at the expense of compression time and memory required, add the `-9` option.

To uncompress an .xz file, add the `-d` option:

```
xz -dv -T4 <filename>.xz
```

Enter `man xz` or `xz -H` for more information and to view all available options.

#### Archiving and compressing with tar
You can also archive and compress with one command using `tar` with the `-z` option, which uses `gzip` compression by default. For example:

```
tar -czvf <filename>.tar.gz <dir>
```

Alternatively, to use `xz` to compress, use the `-J` option instead. In contrast to using `gzip` or `xz` separately, `tar` does not delete the source files by default. Add the `--remove-files` option to do so.

To uncompress and unarchive in one command, use the `-x` option:

```
tar -xvf <filename>.tar.gz
```

This will extract the contents of the archive into the current directory. `tar` will automatically detect which uncompression program to use, and note that it will not automatically delete the compressed archive file after extracting the files.

Software for Linux is typically distributed as a .tar.gz file, so a command like the above will extract the source code or binary files into the current directory.

#### Archiving and compressing before transferring files
Creating and compressing a single archive file can be useful before transferring files to or from SIUE systems, especially for directories with a large number of files (e.g., > 1000, regardless of the total size of those files). Each file has associated metadata, and the transfer can be slowed by attending to that metadata. Compressing files will reduce the amount of data that needs to be transferred. However, it takes time to compress and uncompress files, so the total transfer time may not necessarily decrease depending on factors like network speeds. With fast network speeds, relative to total transfer size, it is typically not worth compressing files.

### Managing file I/O
File input/output (I/O) refers to reading and writing data to disk. The following offers advice on managing I/O for your compute jobs.

First, try to avoid I/O in the first place. Process data and commands in memory where possible, instead of writing to and reading from disk. This will provide the best performance, though the size of the data and subsequent memory requirements may place limits on this strategy.

Second, use the /scratch directories for I/O when needed, all of which are located on high-performance, local **non-shared** file systems.

Third, try to avoid using the local /tmp directories on compute nodes in most cases because these are limited to 1 GB and can be shared with other jobs. To automatically redirect temporary files to another location, set the `TMPDIR` environment variable. For example, create a `tmp` subdirectory in one of your scratch directories and enter the following:

```
export TMPDIR=/scratch/<username>/tmp
```

Including this line in your `~/.bashrc` will automatically set the variable every time you log in. To change the `TMPDIR` on a job-by-job basis, add a similar line to your job scripts.
