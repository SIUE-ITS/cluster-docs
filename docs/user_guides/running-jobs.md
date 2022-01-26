# Running Jobs on SIUE Systems
This guide describes how to reserve compute resources and run and monitor jobs on the SIUE's high-performance computing (HPC) clusters, including campus cluster, using the Slurm job scheduler.

This guide describes how to run and monitor jobs using the command line. Jobs can also be run and monitored using SIUE OnDemand, a web-based access point for SIUE systems. See the [Getting Started with SIUE OnDemand](user_guides/getting-started-ondemand.md) user guide for more information.

### What is a job?
A job consists of all the data, commands, scripts, and programs that will be used to obtain results.

Jobs can be either batch jobs or interactive jobs, but both types have two main components:

- A request for compute resources
- A set of actions to run on those compute resources

A common mistake for new users of HPC clusters is to run heavy workloads directly on a login node (e.g., login.hpc.siue.edu). Unless you are only running a small test, please make sure to run your program as a job. Processes left running on login nodes may be terminated without warning.

### What is a job scheduler?
The Campus cluster is a general-use shared resource. To ensure fair access, we use a job scheduler to manage all requests for compute resources. Specifically, we use the open-source Slurm (Simple Linux Utility for Resource Management) job scheduler that allocates compute resources on clusters for queued, user-defined jobs. It performs the following functions:

- Schedules user-submitted jobs
- Allocates user-requested compute resources
- Processes user-submitted jobs

When users submit jobs with Slurm, the available compute resources are divided among the current job queue using a fairshare algorithm. Using Slurm means your program will be run as a job on a compute node(s) instead of being run directly on the cluster's login node.
