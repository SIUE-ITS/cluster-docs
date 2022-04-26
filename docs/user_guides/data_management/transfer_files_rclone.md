# Transferring Files using rclone

Rclone is an open-source, command-line utility for managing files in cloud storage. Rclone allows users to sync files from a local drive or location to a cloud storage provider like Google Drive, Dropbox, OneDrive, etc. This is useful for backing up, downloading, and synchronizing files stored on a personal computer to more easily accessible cloud solutions.

### Creating an rclone remote connection

Usage of each cloud service requires the setup of a remote connection, or "remote", to that service.

To begin the process of creating a new remote connection, use the command `rclone config`. You should see the following three options in your terminal:

```
No remotes found - make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q>
```

Enter "n" to continue with remote setup.

The shell will then prompt you one-by-one to enter the following details about the new remote:

* name
* Storage
* client_id
* client_secret
* scope
* root_id_folder
* service_account_file

It will then prompt you to choose some configuration settings, and finally provide a URL to use to grant permission for rclone to access your cloud storage account.

The following example shows how to answer these prompts to set up a Google Drive remote connection.

#### Example: Connecting to Google Drive

#### Setup

`name`: To set up a Google Drive connection, name the remote something informative, e.g., "google-drive".

`Storage`: After choosing a name, the next prompt should be a long numbered menu with different storage solutions. Look for Google Drive, number 15. Enter "15" to continue. A list of all possible storage providers can be found [here](https://github.com/rclone/rclone/blob/master/README.md).

`client_id` and `client_secret`: The next prompts are Google Application Client ID `client_id` and Google Application Client Secret `client_secret`. Leave both prompts blank by pressing "Enter" to accept the default values. Rclone's default client ID is shared by all users of rclone, possibly resulting in slow performance. If you're interested in creating a personal client ID instead, more information can be found [here](https://rclone.org/drive/#making-your-own-client-id).

`scope`: When choosing the scope of what files rclone can access in Google Drive, there are several options:

scope options

Choose "1" as `scope` to allow access to all files.

`root_folder_id`: This can be left blank by pressing "Enter" unless you want to specify a different root folder than your default root folder.

`service_account_file`: Leave this blank by pressing "Enter" to use interactive login.

#### Configurations

`Edit advanced config? (y\n)`: Enter "n" for `Edit advanced config? (y\n)`.

`Use auto config`?: Since SIUE clusters do not have access to a web browser, remote setup must be used instead of auto configuration. Enter "n" when prompted `Use auto config?`.

A URL will be generated that can be copied and pasted into your local web browser to give permission to rclone to access your selected Google account's Drive. Once you've navigated to the URL, click "Allow" and copy the generated code.

Paste the code copied from your local web browser into your session's terminal prompt `Enter verification code`.

Enter "n" for `Configure this as a team drive?` if the setup is for personal use or does not require a team drive.

If all the information was entered correctly, you should see something like the following:

```
[<remote name you entered>]
type = drive
scope = drive
token = {"access_token":"<access_token_here>"}
--------------------
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
```

Finish setup by entering "y". Use the "e" or "d" options if the setup was not satisfactory.

Afterwards, the main `rclone config` menu should now have more options for your newly created remote.

Enter "q" to exit the main `rclone config` menu.

### Backing up files

Rclone's `copy` command creates copies of files between a source and a destination (e.g., from the campus cluster to a Google Drive). `copy` is useful because it skips over files that already exist in the destination. It is very similar to `rsync`.

The `copy` command takes the form of:

```
rclone copy source:sourcepath destination:destinationpath
```

#### Optional:

* Using the flag `--update` checks that skipped files in the remote destination have a newer modified time than the file being transferred. This ensures the newest version of files is available in the cloud.
* `--verbose` gives information about every file being transferred. This can create a lot of screen output but may be helpful to diagnose problems.
* `--progress` shows progress through percentage completed and total elapsed time. This can be useful for large data transfers.
* The `--no-traverse` flag prevents rclone from traversing the entire destination directory when copying files. If the remote destination is very large and you are only copying a small number of files from the source, this can save a lot of time.
* Using filters like `--max-age <time>` or `--max-size <size>` will make the copying process more efficient and avoid copying or traversing unwanted files. More details about filtering can be found [here](https://rclone.org/filtering/).

The entire list of `copy` options can be found [here](https://rclone.org/docs/).

An example command to copy files from your home directory to the ProjectDocs folder in your Google Drive is:

```
rclone copy --update "/home/<username>/files" "google-drive:ProjectDocs"
```

where "google-drive" is the name of the remote connection to your Google Drive.

?> Note: If the destination folder (ProjectDocs) does not exist, rclone will create it.

To check if the files have transferred properly, you can manually look online or use the command `rclone ls remote:path`. There will be no terminal confirmation output of successful file transfer by default.

Additionally, `rclone copy` can be used within a regularly executed bash script to emulate a scheduled "backup" to your cloud service.

### Synchronizing files

The difference between copying files and synchronizing files is that `copy` creates duplicates from a source to a destination, but `sync` creates a replica of the source at the destination. In other words, if files are deleted from the source, synchronizing the source and destination will delete files from the destination as well. Copying will never delete files in the destination.

The default synchronization command is:

```
rclone sync source:sourcepath destination:destinationpath
```

An example command to sync your home directory folder to your ProjectDocs folder in your Google Drive is:

```
rclone sync "/home/<username>" "google-drive:ProjectDocs"
```

where "google-drive" is the name of the remote connection to your Google Drive.

Additional flags can be added similar to the `copy` command.

### Navigating remote cloud storage

Rclone can be used to check files on the remote connection, similar to using commands to view local files.

For example, to view the files in your ProjectDocs folder on your "google-drive" remote, an example command could be:

```
rclone ls google-drive:ProjectDocs
```

To create a new subfolder in ProjectDocs:

```
rclone mkdir google-drive:ProjectDocs/Test
```

To delete a file in the remote ProjectDocs folder:

```
rclone deletefile google-drive:ProjectDocs/test.txt
```

### Additional resources

A full list of rclone commands can be found [here](https://rclone.org/commands/).
