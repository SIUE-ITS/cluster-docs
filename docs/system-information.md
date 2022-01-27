# System Information

The following graphic depicts the SIUE ITS Cyberinfrastructure and how different systems interact with one another:

![cyberinfrastructure](user_guides/_media/cyberinfrastructure.png ':size=50%')

### Computing resources

SIUE's campus computing cluster consists of one login node and a total of around 512 CPU cores in around 12 compute nodes. The compute nodes have varying numbers of cores and amounts of memory, and the cluster has a few partitions to organize nodes with specific intended uses. The entire cluster resides on a 100 gigabit ethernet backbone.

For detailed information on campus's computing resources, see the [Campus Resource Overview](user_guides/resource-overview.md).

### File systems

Each user has access to three file systems for their SIUE account: /home, /project, and /scratch.

The /home file system consists of personal directories of 100 GB for each user for their configuration files and personal scripts. The /project file system is a high-performance, parallel I/O file system with a capacity of 8.4 PB of usable space and consists of directories for different research project groups. The scratch file systems — /scratch and /scratch2 — are high-performance, parallel I/O file systems intended for storing temporary files and for I/O-intensive jobs. /scratch has a capacity of 806 TB and /scratch2 has a capacity of 709 TB, with users receiving a 10 TB quota for each file system.

For more information on the different file systems and their intended uses, see the [Storage File Systems user guide](user_guides/storage-file-systems/.md).

### Operating system

The SIUE uses a customized distribution of the Ubuntu Operating System, built using the publicly available Debian Package Manager (APT). Ubuntu is a high-quality Linux distribution that gives SIUE complete control of its open-source software packages and is fully customized to suit advanced research computing needs, without the need for license fees.

SIUE’s distribution of Ubuntu was modified for minor bug fixes and desired localized behavior. Many desktop and clustering-related packages were also added to the SIUE's Ubuntu installation.

A number of white papers, tutorials, FAQs, and other documentation on Ubuntu can be found on the [official Ubuntu website](https://ubuntu.com/tutorials).