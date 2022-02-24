# Running Jobs on SIUE Systems
This guide describes how to reserve compute resources and run and monitor jobs on SIUE's high-performance computing (HPC) clusters, including campus cluster, using the Slurm job scheduler.

This guide describes how to run and monitor jobs using the command line. Jobs can also be run and monitored using SIUE OnDemand, a web-based access point for SIUE systems. See the [Getting Started with SIUE OnDemand](user_guides/hpc_basics/getting_started_ondemand.md) user guide for more information.

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

Jobs also depend on project account allocations, and each job will account for a limit in QOS see [resource overview](user_guides/hpc_basics/resource_overview.md) for more information. You can use the `projects` command to see your available and Slurm accounts and your resources for each. Take note of the slurm_accounts column this will be used for jobs pertaining to their respective project.

```
miwalls@cc-head-01:~$ projects
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
| id |     title     |              description               |    pi   |    directories     |         resources         | slurm_accounts |     unix_group    |
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
| 5  |     Debug     |     Debug project for development.     | miwalls | /project/miwalls_5 |      campus (Cluster)     |   miwalls_5    | project_miwalls_5 |
|    |               |                                        |         |  /bulk/miwalls_5   |                           |                |                   |
+----+---------------+----------------------------------------+---------+--------------------+---------------------------+----------------+-------------------+
```

### Slurm commands

The following table provides a summary of Slurm commands:

| Category | Command | Purpose |
| --- | --- | --- |
| Cluster info | sinfo | Display compute partition and node information |
| Submitting jobs | sbatch | Submit a job script for remote execution |
| | srun | Launch parallel tasks (i.e., job steps) (typically for MPI jobs) |
| | salloc | Allocate resources for an interactive job |
| | squeue | Display status of jobs and job steps |
| | sprio | Display job priority information |
| | scancel | Cancel pending or running jobs |
| Monitoring jobs	| sstat | Display status information for running jobs |
| | sacct | Display accounting information for jobs |
| | seff | Display job efficiency information for past jobs |
| Other | scontrol | Display or modify Slurm configuration and state |

To learn about all the options available for each command, enter `man <command>` for a specific command or see the [official Slurm documentation](https://slurm.schedmd.com/documentation.html).

### Batch jobs

A batch job is a set of actions that is performed remotely on one or more compute nodes. Batch jobs are implemented in job scripts, which are a special type of Bash script that specifies requested resources and a set of actions to perform. The main advantage of batch jobs is that they do not require manual intervention to run properly. This makes them ideal for programs that run for a long time.

The following is a typical batch job workflow:

1. Create job script
1. Submit job script with `sbatch`
1. Check job status with `squeue`
1. When job completes, check log file
1. If job failed, modify job and resubmit (back to step 1)
    - Check job information with `sacct` or `seff` if needed
1. If job succeeded, check job information with `sacct` or `seff`
    - If possible, use less resources next time for a similar job
Below is an example batch job script that runs a Python script:

> Note `<project>` should be used from `slurm_accounts` column output from `projects` command depending on job type if multiple projects.

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=16GB
#SBATCH --time=1:00:00
#SBATCH --account=<project>

module load anaconda3
python script.py
```

The top line:

```
#!/bin/bash
```

Specifies which shell interpreter to use when running your script. The `bash` interpreter is specified, so everything in the job script should be in Bash syntax.

The next set of lines:

```
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=16GB
#SBATCH --time=1:00:00
#SBATCH --account=<project>
```

use Slurm options to specify the requested resources for your job.

Make sure to use the correct account for your jobs (`<project>`). Without the `--account` option, your default project account will be used. This is fine if you only have one project account. Your project IDs can be found in the User Portal on the Allocations page. Project IDs are of the form `<PI_username>_<id>` where `<PI_username>` is the username of the project's Principal Investigator (PI) and `<id>` is a 1 to 3 digit project ID number (e.g., ttrojan_123).

The next set of lines:

```
module load anaconda3
```

loads the required software modules (module load ...).

The final line:

```
python script.py
```

is the command that runs a Python script. More generally, these final lines will be the command or set of commands needed to run your program or multiple programs.

To submit a batch job, use Slurm's `sbatch` command when logged in to the Discovery or Endeavour cluster:

```
sbatch my.job
```

where the argument to the command is the job script's file name (e.g., my.job).

Submitted jobs are sent to the job scheduler, placed in the queue, and then processed remotely when the requested resources become available. The job process is recorded and written to an output file in the same directory that your job script is stored in. By default, this output file is named `slurm-<jobid>.out`. This is a plain-text file, so you can view it using the `less` command:

```
less slurm-<jobid>.out
```

To exit, enter `q`.

If you are submitting jobs to the preempt queue, you will need to specify the `preempt` partition along with if desired `cancel` QOS see [resource overview](user_guides/hpc_basics/resource_overview.md) for more information. Note that `requeue` is the default QOS if not set as below. This can be done using the `--partition` and optionally `--account` options in your job script:

```
#SBATCH --partition=preempt
#SBATCH --qos=cancel
#SBATCH --account=<project>
```

If you encounter an error similar to:

```
Invalid account or account/partition combination specified
```

when submitting a job, double check that you have entered the right partition name and project ID or contact us at its-cluster-support@siue.edu to obtain the correct combination.

### Interactive jobs

An interactive job logs you on to one or more compute nodes where you can work interactively. All actions are performed on the command line. The main advantage of interactive jobs is that you get immediate feedback and the job will not end (and relinquish your compute resources) if your command, script, or program encounters an error and terminates. This is especially useful for developing scripts and programs as well as debugging.

Use Slurm's `salloc` command to reserve resources on a node:

```
user@cc-head-01:~$ salloc --time=2:00:00 --cpus-per-task=8 --mem=16GB --account=<project>
salloc: Pending job allocation 324316
salloc: job 324316 queued and waiting for resources
salloc: job 324316 has been allocated resources
salloc: Granted job allocation 324316
salloc: Waiting for resource configuration
salloc: Nodes cc-cpu-01 are ready for job
```

Make sure to change the resource requests (the `--time=2:00:00 --cpus-per-task=8 --mem=16GB --account=<project>` part after your `salloc` command) as needed, such as the number of cores and memory required. Also make sure to substitute `<project>` for your project Slurm account as noted in `projects` command output.

Once you are granted the resources and logged in to a compute node, you can then begin entering commands (such as loading software modules):

```
user@cc-cpu-01:~$ module load conda
user@cc-cpu-01:~$ python --version
Python 3.9.2
user@cc-cpu-01:~$
```

Notice that the shell prompt has changed from `user@cc-head-01` to `user@<nodename>` to indicate that you are now on a compute node (e.g., cc-cpu-01).

To exit the node and relinquish the job resources, enter `exit` in the shell. This will return you to the login node:

```
user@cc-cpu-01:~$ exit
exit
salloc: Relinquishing job allocation 324316
user@cc-head-01:~$
```

### Resource requests

Slurm allows you to specify many different types of resources. The following table describes the more common resource request options and their default values:

| Resource | Default value | Description |
| --- | --- | --- |
| \--nodes=<number> | 1 | Number of nodes to use |
| \--ntasks=<number> | 1 | Number of processes to run |
| \--cpus-per-task=<number> | 1 | Number of cores per task |
| \--mem=<number> | N/A | Total memory (per node) |
| \--mem-per-cpu=<number> | 2GB | Memory per processor core |
| \--constraint=<attribute> | N/A | Node property to request (e.g., xeon-4116) |
| \--partition=<partition_name> | general | Request nodes on specified partition (typically general or preempt) |
| \--qos=<qos_name> | N/A | Only required to be set if you would like `cancel` QOS `preempt` partition or `debug` QOS `general` partition |
| `--time=<D-HH:MM:SS>` | 30 days `30-00:00:00` | Maximum run time |
| \--account=<project_ID> | default project account | Account to run resources |

If the resource option is not specified, the default value will be used.

SIUE compute nodes have varying numbers of cores and amounts of memory. For more information on node specs, see the Discovery Resource Overview or Endeavour Resource Overview.

#### Nodes, tasks, and CPUs

The default value for the `--nodes` option is 1. This number should typically be increased if you are running a parallel job using MPI, but otherwise it should be unchanged. The default value for `--ntasks` is also 1, and it should typically be increased if running a multi-node parallel job.

The `--cpus-per-task` option refers to logical CPUs. On SIUE compute nodes, there are typically two physical processors (sockets) per node with multiple cores per processor, such that 1 core = 1 thread = 1 logical CPU. These terms may be used interchangeably. Nodes on the `general` and `preempt` partition have varying numbers of cores (32, 64). This option should be changed depending on the nature of your job. Serial jobs only require 1 core, the default value. Additionally, single-threaded MPI jobs only require 1 core per task. For multi-threaded jobs of any kind, the value should be increased as needed to take advantage of multiple cores.

For more information on MPI jobs, see the [Using MPI user guide](user_guides/software_and_programming/using_mpi.md).

#### Memory

The `#SBATCH --mem=0` option tells Slurm to reserve all of the available memory on each compute node requested. Otherwise, the max memory (`#SBATCH --mem=<number>`) or max memory per CPU (`#SBATCH --mem-per-cpu=<number>`) can be specified as needed.

> Note that some memory on each node is reserved for system overhead. Here is a scenario where you may forget about considering memory overhead:
>
> A compute node consisting of 32 CPUs with specs stating 256 GB of shared memory really has ~240 GB of usable memory. You may tabulate "256 GB / 32 CPUs = 8 GB per CPU" and add `#SBATCH --mem-per-cpu=8GB` to your job script. Slurm may alert you to an incorrect memory request and not submit the job.
>
> In this case, setting `#SBATCH --mem-per-cpu=7GB` or `#SBATCH --mem=0` or some value less than 240 GB will resolve this issue.

#### GPUs

To request a GPU on campus's general partition, add the following line to your Slurm job script:

Also add one of the following `sbatch` options to your Slurm job script to request the type and number of GPUs you'd like to use:

```
#SBATCH --gres=gpu:<number>
```

or

```
#SBATCH --gres=gpu:<gpu_type>:<number>
```

where:

- `<number>` is the number of GPUs per node requested, and
- `<gpu_type>` is one of the following: a100.

For more information, see the [Using GPUs user guide](user_guides/software_and_programming/using_gpus.md).

### Queue times

Generally, if you request a lot of resources or very specific, limited resources, you can expect to wait longer for Slurm to assign resources to your job. Additionally, if the campus cluster is especially active, you can also expect to wait longer for Slurm to assign resources to your job.

### Job monitoring

There are a number of ways to monitor your jobs, either with Slurm or other tools.

#### Job monitoring with Slurm

After submitting a job, there are a few different ways to monitor its progress with Slurm. The first is to check the job queue with the `squeue` command. For example:

```
squeue -u $USER
```

The `-u` option allows you to specify only jobs for a given username.

The output will look similar to the following:
```
  JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
4822945      main     test  cougar  PD       0:00      1 (Priority)
```

It provides the following information:

| Output | Definition |
| JOBID | Unique numeric ID assigned to each job |
| PARTITION | Partition the job is running on |
| NAME | Job name (by default, the filename of the job script) |
| USER | User that submitted the job |
| ST | Current state of the job (see table below) |
| TIME | Amount of time the job has been running |
| NODES | Number of nodes the job is running on |
| NODELIST(REASON) | If running, the list of nodes the job is running on. If pending, the reason the job is waiting. |

The information that `squeue` returns can be customized; refer to the `squeue` manual for more information.

The `ST` column refers to the state of the job. The following table provides common codes:

| Code | State	Meaning |
| PD | Pending	Job is pending (e.g., waiting for requested resources) |
| R | Running	Job is running |
| CG | Completing	Job is completing |
| CD | Completed	Job has completed |
| CA | Cancelled	Job was cancelled |

When a job is running, you can use the `sstat` command to monitor the job for specific values of interest at the time the command is entered. For example, to focus on a few important values, use a command like the following:

```
sstat -j <jobid> --format=JobID,MaxRSS,AveCPUFreq,MaxDiskRead,MaxDiskWrite
```

When a job is running or completed, you can use the `sacct` command to obtain accounting information about the job. Entering `sacct` without options will provide information for your jobs submitted on the current day, with a default output format. To focus on values similar to those from `sstat` after a job has completed, use a command like the following:

```
sacct -j <jobid> --format=JobID,MaxRSS,AveCPUFreq,MaxDiskRead,MaxDiskWrite,State,ExitCode
```

Once a job has completed, you can also use the `seff` command to obtain job efficiency information (CPU and memory efficiency). For example:

```
seff <jobid>
```

#### Job monitoring from output

Most programs will generate some form of output when run. This can be in the form of status messages sent to the terminal or newly generated files. If running a batch job, Slurm will redirect output meant for the terminal to an output file of the form `slurm-<jobid>.out`. View the contents of this file to see output from the job. If needed, you can also run your programs through profiling or process monitoring tools in order to generate additional output.

#### Job monitoring on compute nodes

When your job is actively running, the output from `squeue` will report the compute node(s) that your job has been allocated. You can log on to these nodes using the command `ssh <nodename>` from one of the login nodes and then use a process monitoring tool like `top`, `htop`, `atop`, or `iotopin` order to check the CPU, memory, or I/O usage of your job processes.

!> You cannot login to a node that you do not have a running job on. There will be a denied error upon login if no running jobs are found by your user.

### Job exit codes

Slurm provides exit codes when a job completes. An exit code of 0 means success and anything non-zero means failure. The following is a reference guide for interpreting these exit codes:

| Code | Meaning | Note |
| --- | --- | --- |
| 0 | Success | |
| 1 | General failure | |
| 2 | Incorrect use of shell builtins | |
| 3-124 | Some error in job | Check software exit codes |
| 125 | Out of memory | |
| 126 | Command cannot execute | |
| 127 | Command not found | |
| 128 | Invalid argument to exit | |
| 129-192 | Job terminated by Linux signals | Subtract 128 from the number and match to signal code. Enter `kill -l` to list signal codes and enter `man signal` for more information. |

### Slurm environment variables

Slurm creates a set of environment variables when jobs run. These can be used in job or application scripts to dynamically create directories, assign the number of threads, etc. depending on the job specification. Some example variables:

| Variable | Description |
| --- | --- |
| SLURM_JOB_ID | The ID of the job allocation |
| SLURM_JOB_NODELIST | List of nodes allocated to the job |
| SLURM_JOB_NUM_NODES | Total number of nodes in the job's resource allocation |
| SLURM_NTASKS | Number of tasks requested |
| SLURM_CPUS_PER_TASK | Number of CPUs requested per task |
| SLURM_SUBMIT_DIR | The directory from which `sbatch` was invoked |
| SLURM_ARRAY_TASK_ID | Job array ID (index) number |

For example, to assign OpenMP threads, you could include a line like the following in your job script:

```
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
```

### Additional resources

- [Slurm documentation](https://slurm.schedmd.com/)
- [Slurm Quick Start User Guide](https://slurm.schedmd.com/quickstart.html)
- [Slurm tutorials](https://slurm.schedmd.com/tutorials.html)
- [Slurm cheatsheet](https://slurm.schedmd.com/pdfs/summary.pdf)
- [Slurm Job Script Templates](user_guides/hpc_basics/script_templates.md)
