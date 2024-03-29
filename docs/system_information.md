# System Information

The following graphic depicts the SIUE ITS Cyberinfrastructure and how different systems interact with one another:

![cyberinfrastructure](_media/cyberinfrastructure.png)

### Computing resources

SIUE's campus computing cluster consists of one login node and a total of around 672 CPU cores and 9 A100 GPUs on 15 compute nodes. The compute nodes have varying numbers of cores and amounts of memory, and the cluster has a few partitions to organize nodes with specific intended uses. The entire cluster resides on a 100 and 25 gigabit ethernet backbone.

For detailed information on campus's computing resources, see the [Campus Resource Overview](user_guides/hpc_basics/resource_overview.md).

### File systems

Each user has access to three file systems for their SIUE account: `/home`, `/project`, `/bulk`, and `/scratch`.

The `/home` file system consists of personal directories of 100 GB for each user for their configuration files and personal scripts. The `/project` file system is a high-performance, parallel CEPH file system with a capacity of 100 TB of usable space and consists of directories for different research project groups. `/bulk` is like project except on a higher capacity lower-performance CEPH file system. The scratch file system `/scratch` is a temporary storage directory located on every compute node which is local and **not** shared across nodes. Any files older than a week are automatically deleted. The storage space available varies between 500 GB to 1 TB.

For more information on the different file systems and their intended uses, see the [Storage File Systems user guide](user_guides/data_management/storage_file_systems.md).

### Operating system

SIUE uses a customized distribution of the Ubuntu Operating System, built using the publicly available Debian Package Manager (APT). Ubuntu is a high-quality Linux distribution that gives SIUE complete control of its open-source software packages and is fully customized to suit advanced research computing needs, without the need for license fees.

SIUE’s distribution of Ubuntu was modified for minor bug fixes and desired localized behavior. Many desktop and clustering-related packages were also added to the SIUE's Ubuntu installation.

A number of white papers, tutorials, FAQs, and other documentation on Ubuntu can be found on the [official Ubuntu website](https://ubuntu.com/tutorials).
