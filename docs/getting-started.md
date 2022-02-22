# Getting Started

The information on this page is designed to provide an introductory overview for new SIUE users. Links to more detailed information and complete user guides can be found at the end of each section of this page.

### What is the SIUE ITS Cyberinfrastructure?

SIUE ITS Cyberinfrastructure provides advanced computational research support to SIUE faculty and students. When the needs of researchers exceed the capabilities of a personal computer, SIUE offers support. Examples of services offered include high-performance computing, data storage and analysis, and research computing workshops.

Currently, SIUE supports more than 10 research groups across a variety of disciplines in the SIUE community.

For more information, see our [About](https://www.siue.edu/its/cyberinfrastructure/) page.

### What is high-performance computing?

High-performance computing aggregates the resources from individual computers (known as nodes) into a cluster that works together to perform advanced, specialized computing jobs. Many academic fields, including epigenetics, geophysics, materials science, engineering, natural language translation, and health sciences, utilize high-performance computing to advance their research beyond what would be possible with a personal computer.

As the amount of data used in research continues to grow with the popularity of such technologies as artificial intelligence (AI) and advanced data analysis, high-performance computing is becoming increasingly necessary for technological advancement.

### Introduction to SIUE systems

#### Campus cluster

Campus is a high-performance computing cluster, which is a collection of computers and disk arrays that are connected via fast networks. Campus allows SIUE researchers to perform computing tasks, like data analyses and simulations, on a larger scale than is possible with a laptop or lab computer. The campus cluster is SIUE's public cluster; any SIUE user can use campus.

The following graphic depicts the SIUE ITS Cyberinfrastructure and how different systems interact with one another:

![cyberinfrastructure#center](user_guides/_media/cyberinfrastructure.png)

For detailed information on the campus cluster, see our [Getting Started](user_guides/getting-started.md) user guide and our [System Information](system-information) page.

#### Storage file systems

All SIUE account holders are assigned three directories where they can store files and run programs. Each of these three directories is located in the home, project, and scratch file systems.

Each file system serves a different purpose:

/home is a network filesystem using CephFS and hosted on the NVME Ceph Cluster system for storing configuration files and personal scripts. Each SIUE user has a 100 GB and 1 million files home directory quota.

/project: Assignment to a project gives you access to a subdirectory of the /project parallel file system. Managed by a Principal Investigator, this is where you have access to 1000 GB or more of storage space (shared among the project's members), and where you can collaborate and share files with your research group. Use this high-performance file system for most of your research computing work at SIUE. If more than 1000 GB of storage is needed, a change request can be made in the user portal. See the allocation request change user guide for more information. !~~~~~ allocation change

/scratch is a temporary storage directory located on every compute node which is local and **not** shared across nodes. Any files older than a week are automatically deleted.

For detailed information on the different storage systems available, see our [Storage File Systems user guide](user_guides/storage-file-systems) and our [System Information page](system-information).

### Accessing SIUE systems

All SIUE students, staff, and faculty have access to SIUE's systems for their research projects. In order to access SIUE's systems, you must either be the Principal Investigator (PI) of a research project or an authorized member of a PI's research project.

SIUE systems are accessed using your SIUE e-ID and password, so there are no additional requirement for SIUE-specific account creation.

Note: Your SIUE e-ID is the first part of your SIUE email address (e.g., cougar@siue.edu's e-ID is cougar).

Access to and use of resources is based on participation in projects. Project setup and resource allocation requests must be made by a project's PI using the [SIUE User Portal](https://coldfront.hpc.siue.edu).

For more information on accounts, see our [Accounts and Allocations page](accounts-and-allocations).

### How to use the systems

When using SIUE systems, you will notice several differences from your desktop or laptop environment:

- The interface is command-line driven (no graphical user interface)
- The systems use the Ubuntu Linux operating system (not macOS or Windows)
- You submit your programs to a remote batch processing system, or job scheduler, to run them

SIUE has prepared user guides specific to campus systems in the following categories:

[HPC Basics](user_guides/hpc-basics)
How to log in to Discovery and Endeavour and run jobs.

[Data Management](user_guides/data-management)
An overview of the different storage directories available to you, and instructions on transferring files between your personal computer and SIUE systems.

[Software and Programming](user_guides/software-and-programming)
Information on the software available on SIUE systems, as well as instructions for installing your own software.

[Project and Allocation Management](user_guides/project-and-allocation-management)
How to create and manage your projects and resource allocations.

Our main user guides page can be accessed [here](user-guides).

### How to get help

#### Discourse user forum

SIUE uses Microsoft Teams, a question-and-answer community channel, to facilitate discussion and knowledge sharing among users. The channel is a great resource for discussing SIUE-related topics, asking non-urgent technical questions, and sharing ideas. The cyberinfrastructure team monitors the forum for questions, but users are also encouraged to interact with each other.

You can access SIUE's Teams Channel [here](https://bit.ly/3HZYqHI).

#### User support

If you're experiencing an issue with system resources or your SIUE account and you're unable to find a solution to your problem using the above resources, please reference [here](user-support) for support information.
