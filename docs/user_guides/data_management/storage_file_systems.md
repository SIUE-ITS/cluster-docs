# Storage File Systems
All SIUE account holders are assigned two directories where they can store files and run programs: home and project. These file systems are global in that you can access them from the campus cluster or transfer node.

!> No filesystem on the campus cluster is backed up and users must come up with their own backup system.

The following table provides an overview of the file system use recommendations:

| File system	| Recommended storage	| Recommended activities |
| --- | --- | --- |
| /home |	personal files, configuration files	| creating and editing scripts/programs |
| /project | shared files, software installations, job scripts and files, I/O files |	creating and editing scripts/programs, compiling, I/O, transferring data |
| /bulk | large files, archives |	files with low performance requirements or that take up lots of space |
| /scratch | File I/O intensive jobs | jobs that don't require shared filesystems and need a location for temporary space for computation |

### Home file system
The `/home` file system consists of personal directories for users, running CephFS and hosted on the NVME Ceph Cluster. Your home directory has a quota of 100 GB of disk space and 1 million files. It is intended for storing personal and configuration files. It is not intended for I/O-intensive jobs.

When you log in, you will always start in your home directory, which is located at:

> ```/home/<username>```

Use the `cd` command to quickly change to your home directory from another directory.

### Project file system
Project directories are by request only. To request a project directory request a [project storage resource allocation](user_guides/project_and_allocation_management/request_new_allocation).

The `/project` file system consists of directories for different research project groups. It offers high-performance, parallel I/O, running CephFS and hosted on the NVME Ceph Cluster. The default quota for each project directory is 1000GB of disk space and 10 million files. More information about requesting an increase in storage allocation can be found at the [storage request increase guide](user_guides/project_and_allocation_management/request_storage_increase.md).

Each project member has access to their project's project directory, where they can store data, scripts, and related files. The project directory should be used for most of your SIUE work, and it's also where you can collaborate with your research project group. Users affiliated with multiple SIUE projects will have access to multiple project directories so they can easily share their files with the appropriate groups.

Project directories are located at:

?> To see your available project directories type `projects` in a shell and look at the directories column.

Example `projects` output.
```
miwalls@cc-head-01:~$ projects
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
| id |     title     |              description               |    pi   |    directories     |         resources         | slurm_accounts |     unix_group    |
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
| 5  |     Debug     |     Debug project for development.     | miwalls | /project/miwalls_5 |      campus (Cluster)     |   miwalls_5    | project_miwalls_5 |
|    |               |                                        |         |  /bulk/miwalls_5   |                           |                |                   |
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
```

> ```/project/<project>```

where `<project>` would be `miwalls_5` from the example `projects` output above.

?> Tip: You can create an alias command to quickly change to your project directory. For example, for the user cougar, adding the line `alias cdp="cd /project/<project>"` to their `~/.bashrc` file will create the command cdp every time they log in, which can be used as a shortcut for quickly switching to their project directory.

To create your own subdirectory within your project's directory, enter a command like the following:

> ```mkdir /project/<project>/<username>```

If needed, you can change the permissions of this subdirectory using a `chmod` or `setfacl` command.

### Bulk file system
Bulk directories are by request only. To request a bulk directory request a [bulk storage resource allocation](user_guides/project_and_allocation_management/request_new_allocation.md).

The `/bulk` file system consists of directories for different research bulk groups. It offers HDD, parallel I/O, running CephFS and hosted on the HDD Ceph Cluster. The default quota for each bulk directory is 1000GB of disk space and 10 million files. More information about requesting an increase in storage allocation can be found at the [storage request increase guide](user_guides/project_and_allocation_management/request_storage_increase.md).

Each project member has access to their project's bulk directory, where they can large files and archives. The bulk directory should be used for most of your SIUE less performant storage needs, and it's also where you can collaborate with your research project group. Users affiliated with multiple SIUE projects will have access to multiple bulk directories so they can easily share their files with the appropriate groups.

Bulk directories are located at:

?> To see your available bulk directories type `projects` in a shell and look at the directories column.

Example `projects` output.

```
miwalls@cc-head-01:~$ projects
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
| id |     title     |              description               |    pi   |    directories     |         resources         | slurm_accounts |     unix_group    |
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
| 5  |     Debug     |     Debug project for development.     | miwalls | /project/miwalls_5 |      campus (Cluster)     |   miwalls_5    | project_miwalls_5 |
|    |               |                                        |         |  /bulk/miwalls_5   |                           |                |                   |
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
```

> ```/bulk/<project>```

where `<project>` would be `miwalls_5` from the example `projects` output above.

?> Tip: You can create an alias command to quickly change to your bulk directory. For example, for the user cougar, adding the line `alias cdp="cd /bulk/<project>"` to their `~/.bashrc` file will create the command cdp every time they log in, which can be used as a shortcut for quickly switching to their bulk directory.

To create your own subdirectory within your bulk's directory, enter a command like the following:

> ```mkdir /bulk/<project>/<username>```

If needed, you can change the permissions of this subdirectory using a `chmod` or `setfacl` command.

### Using scratch space

Every compute node has a `/scratch` directory which is **not** shared. They are unique and local only to the compute nodes. Any files older than a week are automatically deleted.

The scratch directory is useful for jobs that require temporary files or high disk IO where a network storage would result in a significant slowdown.

> ```export TMPDIR=/scratch/<username>```

You can add this line to your `~/.bash_profile` to automatically set this variable when logging in.

`/scratch` has a capacity of 500GB to 1TB depending on the node. These directories should only be used for temporary files that are dependent on a currently running job.

### Limits on disk space and number of files

SIUE clusters are shared resources. As a result, there are quotas on usage to help ensure fair access to all SIUE researchers. There are quotas on both the amount of disk space used and the number of files stored.

To check your quota, use `quotacheck`. Compare the results of `Usage` and `Limit`. If the value of `Usage` is close to the value of `Limit`, you will need to delete, compress, consolidate, or archive files. For certain directories you can also request an increase in disk space from the User Portal, more information at [storage request increase guide](user_guides/project_and_allocation_management/request_storage_increase.md).

```
miwalls@cc-head-01:~$ quotacheck
+--------------------+----------------+-----------------+-----------------+----------------+----------------+----------------+
|     Directory      | Disk Usage (%) | Disk Usage (GB) | Disk Limit (GB) | File Usage (%) | File Usage (#) | File Limit (#) |
+--------------------+----------------+-----------------+-----------------+----------------+----------------+----------------+
|   /home/miwalls    |      9.95      |      97.13      |      976.56     |     41.27      |     412702     |    1000000     |
| /project/miwalls_5 |      0.0       |       0.0       |      104.86     |      0.0       |       0        |    1000000     |
+--------------------+----------------+-----------------+-----------------+----------------+----------------+----------------+
```

If you exceed the limits, you will receive a `disk quota exceeded` error and message when reaching 90% full on the [SIUE OnDemand](https://ondemand.hpc.siue.edu/) home page.

![OnDemand Quota Limit Warning](_media/storage_file_systems/quota-limit-warning.png)
