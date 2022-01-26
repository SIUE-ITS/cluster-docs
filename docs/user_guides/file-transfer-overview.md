# File Transfer Overview
Secure and efficient data transfer to and from SIUE systems can be achieved with a variety of useful tools, the choice of which depends on whether the storage location is a personal computer or an external site (e.g., cloud storage). The choice also depends heavily on the familiarity of the user. There are three methods of data transfer that we recommend for use with SIUE systems: **command-line tools**, **graphical tools**, and the **Globus service**.

### Dedicated data transfer nodes
SIUE has one dedicated, high-speed, 40 Gbps data transfer nodes at `dtn.hpc.siue.edu`. This node is especially useful for larger transfers. If needed, you can log in to them using, for example, `ssh <e-ID>@dtn.hpc.siue.edu`. The campus cluster login nodes has a 100 Gbps connection speed and is adequate for most transfers.

?> Note: Your transfer speeds are determined by a number of factors, such as the network speed at your location, router and firewall settings, etc. If you experience slower than expected transfers, try to troubleshoot these issues first.

### Command-line tools
There are a number of command-line interface (CLI) tools to transfer data to and from SIUE storage systems, such as sftp, rsync, and rclone. Each is targeted to specific use cases.

For more information, see the guide for [Transferring Files Using the Command Line](user_guides/transfer-files-command-line.md).

### Graphical tools
Applications such as Cyberduck, FileZilla, and WinSCP provide a graphical user interface (GUI) to transfer data between a personal computer and a storage solution that allows SFTP connections, including SIUE storage systems. These applications offer drag-and-drop capability, but transfer speeds may be slower compared to using a command-line tool.

For more information, see the guide for [Transferring Files Using a Graphical User Interface](user_guides/transfer-files-gui.md).

### Globus service
Globus is a data management and transfer service that gives researchers unified access to their data across systems through a web-based GUI. It can be used for data transfers from a personal computer or another HPC center to SIUE storage systems. Relative to other tools, it is useful for large transfers and will provide the best transfer speeds. A CLI for Globus can also be used if desired.

For more information, see the guide for [Transferring Files Using Globus](user_guides/transfer-files-globus.md).

### Which method should I use?
Below are four example scenarios that provide some insight into which data transfer method you might use for a given situation:

| System 1 | System 2	| Example Scenarios	| Method |
| --- | --- | --- | --- |
| Personal computer	| SIUE file system for small-medium transfers	| When transferring files from a personal computer to your SIUE project folder that takes a moderate amount of time	| GUI, CLI |
| Personal computer |	SIUE file system for large or secure transfers | When transferring files from a personal computer to your SIUE project directory that takes a large amount of time | Globus |
| Amazon Web Services (AWS)	| Any SIUE file system	| When transferring files from an AWS server to your SIUE project directory	| CLI |
| Other HPC center | Any SIUE file system	| When transferring files from another university or research institution to your SIUE project directory | Globus |
