# Getting Started

The information on this page is designed to provide an introductory overview for new SIUE users. Links to more detailed information and complete user guides can be found at the end of each section of this page.

### What is the SIUE ITS Cyberinfrastructure?

SIUE ITS Cyberinfrastructure provides advanced computational research support to SIUE faculty and students. When the needs of researchers exceed the capabilities of a personal computer, SIUE offers support. Examples of services offered include high-performance computing, data storage and analysis, and research computing workshops.

Currently, SIUE supports more than 10 research groups across a variety of disciplines in the SIUE community.

For more information, see our Mission, People, and Partnerships pages.

### What is high-performance computing?

High-performance computing aggregates the resources from individual computers (known as nodes) into a cluster that works together to perform advanced, specialized computing jobs. Many academic fields, including epigenetics, geophysics, materials science, engineering, natural language translation, and health sciences, utilize high-performance computing to advance their research beyond what would be possible with a personal computer.

As the amount of data used in research continues to grow with the popularity of such technologies as artificial intelligence (AI) and advanced data analysis, high-performance computing is becoming increasingly necessary for technological advancement.

### Introduction to SIUE systems

#### Campus cluster

Campus is a high-performance computing cluster, which is a collection of computers and disk arrays that are connected via fast networks. Discovery allows SIUE researchers to perform computing tasks, like data analyses and simulations, on a larger scale than is possible with a laptop or lab computer. The campus cluster is the SIUE's public cluster; any SIUE user can use campus.

The following graphic depicts the SIUE ITS Cyberinfrastructure and how different systems interact with one another:

![cyberinfrastructure](user_guides/_media/cyberinfrastructure.png ':size=50%')

For detailed information on the campus cluster, see our [Getting Started](user_guides/getting-started.md) user guide and our System Information page.

#### Storage file systems

All SIUE account holders are assigned three directories where they can store files and run programs. Each of these three directories is located in the home, project, and scratch file systems.

Each file system serves a different purpose:

/home1 is a network file system for storing configuration files and personal scripts. Each SIUE user has a 100 GB home directory quota.

/project: Assignment to a project gives you access to a subdirectory of the /project parallel file system. Managed by a Principal Investigator, this is where you have access to a maximum of 10 TB of storage space (shared among the project's members), and where you can collaborate and share files with your research group. Use this high-performance file system for most of your research computing work at SIUE. If more than 10 TB of storage is needed, it can be purchased in 5 TB increments for $40/TB/year. See the Accounts and Allocations page for more information.

/scratch and /scratch2 are two parallel file systems that are shared among all SIUE users. These file systems can be used for storing data temporarily and running I/O intensive jobs. Each SIUE user receives a 10 TB quota for /scratch and a 10 TB quota for /scratch2.

For detailed information on the different storage systems available, see our Storage File Systems user guide and our System Information page.

### Accessing SIUE systems

All USC students, staff, and faculty have access to the SIUE's systems for their research projects. In order to access the SIUE's systems, you must either be the Principal Investigator (PI) of a research project or an authorized member of a PI's research project.

SIUE systems are accessed using your USC NetID and password, so there is no additional requirement for SIUE-specific account creation.

Note: Your USC NetID is the first part of your USC email address (e.g., ttrojan@usc.edu's NetID is ttrojan).

Duo two-factor authentication is also required. If you have not already signed up for Duo on your USC NetID account, please visit this page to enroll.

Currently, SIUE systems do not support the use or storage of sensitive data. If your research work includes sensitive data, including but not limited to HIPAA-, FERPA-, or CUI-regulated data, see our Secure Computing user guides or contact us at SIUE-support@usc.edu before using our systems.

Access to and use of resources is based on participation in projects. Project setup and resource allocation requests must be made by a project's PI using the SIUE User Portal.

For more information on accounts, see our Accounts and Allocations page.

### How to use the systems

When using SIUE systems, you will notice several differences from your desktop or laptop environment:

The interface is command-line driven (no graphical user interface)
The systems use the CentOS Linux operating system (not macOS or Windows)
You submit your programs to a remote batch processing system, or job scheduler, to run them
The SIUE has prepared user guides specific to its systems in the following categories:

HPC Basics
How to log in to Discovery and Endeavour and run jobs.

Data Management and File Transfers
An overview of the different storage directories available to you, and instructions on transferring files between your personal computer and SIUE systems.

Software and Programming
Information on the software available on SIUE systems, as well as instructions for installing your own software.

SIUE User Portal
How to create and manage your projects and resource allocations.

Our main user guides page can be accessed here.

### Resources for deeper learning

#### Workshop classes

Each month, the SIUE's Research Facilitation & Applications team offers a number of free workshops designed to introduce users to the computing cluster and its features. The team is frequently developing new workshops, including workshops on useful software and programming languages.

All workshops are approximately two hours in length and are offered several times a year.

For information on the different workshops that the SIUE's Research Facilitation & Applications team offers on a rotating basis, see our Seminars and Workshops page. For a schedule of upcoming workshops, see our Events page.

#### External tools and resources

If you want to improve your overall understanding of research computing, the SIUE has compiled a list of external resources that may be helpful for learning about research computing, programming languages, and software used by SIUE systems.

You can view our External Tools and Resources page here.

### How to get help

#### Discourse user forum

The SIUE uses Discourse, a question-and-answer community forum, to facilitate discussion and knowledge sharing among users. The User Forum is a great resource for discussing SIUE-related topics, asking non-urgent technical questions, and sharing ideas. The SIUE team monitors the forum for questions, but users are also encouraged to interact with each other.

You can access the SIUE's User Forum here.

#### Ticket submission

If you're experiencing an issue with system resources or your SIUE account and you're unable to find a solution to your problem using the above resources, please submit a help ticket to the SIUE team.
