# Storage File Systems
All SIUE account holders are assigned two directories where they can store files and run programs: home and project. These file systems are global in that you can access them from the campus cluster or transfer node.

The following table provides an overview of the file system use recommendations:

| File system	| Recommended storage	| Recommended activities |
| --- | --- | --- |
| /home |	personal files, configuration files	| creating and editing scripts/programs |
| /project | shared files, software installations, job scripts and files, I/O files |	creating and editing scripts/programs, compiling, I/O, transferring data

### Home file system
The `/home file` system consists of personal directories for users, running CephFS and hosted on the NVME Ceph Cluster. Your home directory has a quota of 10 GB of disk space and 100 thousand files. It is intended for storing personal and configuration files. It is not intended for I/O-intensive jobs.

When you log in, you will always start in your home directory, which is located at:

> ```/home/<username>```

Use the `cd` command to quickly change to your home directory from another directory.

We keep one week of daily snapshots for /home. You can think of these snapshots as semi-backups, meaning that if you accidentally deleted some data we would be able to recover it within one week. If the file was created and deleted within a one-day period, then the snapshot might not be recoverable. You should always keep extra backups of your important data and other files because of this.

If you need to recover a deleted file, please contact the SIUE team by emailing its-cluster-support@siue.edu.

### Project file system
Project directories are by request only. To request a project directory contact its-cluster-support@siue.edu.

The `/project` file system consists of directories for different research project groups. It offers high-performance, parallel I/O, running CephFS and hosted on the NVME Ceph Cluster. The default quota for each project directory is 100GB of disk space and 1 million files.

Each project member has access to their group's project directory, where they can store data, scripts, and related files. The project directory should be used for most of your SIUE work, and it's also where you can collaborate with your research project group. Users affiliated with multiple SIUE projects will have access to multiple project directories so they can easily share their files with the appropriate groups.

Project directories are located at:

> ```/project/<project_name>```

where `<project_name>` is the name of the project originally requested.

?> Tip: You can create an alias command to quickly change to your project directory. For example, for the user cougar, adding the line `alias cdp="cd /project/<project_name>"` to their `~/.bashrc` file will create the command cdp every time they log in, which can be used as a shortcut for quickly switching to their project directory.

To create your own subdirectory within your project's directory, enter a command like the following:

> ```mkdir /project/<project_name>/<username>```

If needed, you can change the permissions of this subdirectory using a `chmod` or `setfacl` command.

We keep one week of daily snapshots for your project directories. You can think of these snapshots as semi-backups, meaning that if you accidentally deleted some data we would be able to recover it within one week. If the file was created and deleted within a one-day period, then the snapshot might not be recoverable. You should always keep extra backups of your important data and other files because of this.

If you need to recover a deleted file, please contact the SIUE team by emailing its-cluster-support@siue.edu.

### Using scratch space

Every compute node has a `/scratch` directory which is **not** shared. They are unique and local only to the compute nodes. Any files older than a week are automatically deleted.

The scratch directory is useful for jobs that require temporary files or high disk IO where a network storage would result in a significant slowdown.

> ```export TMPDIR=/scratch/<username>```

You can add this line to your `~/.bash_profile` to automatically set this variable when logging in.

`/scratch` has a capacity of 500GB to 1TB depending on the node. These directories should only be used for temporary files that are dependent on a currently running job.

### Limits on disk space and number of files

SIUE clusters are shared resources. As a result, there are quotas on usage to help ensure fair access to all SIUE researchers. There are quotas on both the amount of disk space used and the number of files stored.

To check your quota, use `/software/tools/quota_check.py`. Compare the results of `Usage` and `Limit`. If the value of `Usage` is close to the value of `Limit`, you will need to delete, compress, consolidate, or archive files. For certain directories you can also request an increase in disk space from the User Portal, more information at [storage request increase guide](user_guides/project_and_allocation_management/request_storage_increase.md).

```
miwalls@cc-head-01:~$ /software/tools/quota_check.py
+--------------------+-----------------+-----------------+------------+------------+
|     Directory      | Disk Usage (GB) | Disk Limit (GB) | File Usage | File Limit |
+--------------------+-----------------+-----------------+------------+------------+
|   /home/miwalls    |      515.89     |      104.86     |   743927   |  1000000   |
| /project/miwalls_5 |       0.0       |      104.86     |     0      |  1000000   |
+--------------------+-----------------+-----------------+------------+------------+
```

If you exceed the limits, you will receive a `disk quota exceeded` error and message when reaching 90% full on the [SIUE OnDemand](https://ondemand.hpc.siue.edu/) home page.

![OnDemand Quota Limit Warning](_media/storage_file_systems/quota-limit-warning.png)
