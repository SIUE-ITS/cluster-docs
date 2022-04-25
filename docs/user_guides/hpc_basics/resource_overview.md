# Campus Cluster Resource Overview

SIUE's general use high-performance computing cluster, campus, has over 15 compute nodes available for users to run their jobs on.

For general SIUE system specifications, see our [Getting Started](user_guides/hpc_basics/getting_started.md) page.

### Partitions

There are a few Slurm partitions available on the campus cluster, each with a separate job queue. These are general-use partitions available to all researchers. The table below describes the intended purpose for each partition.

| Partition | Description | Default QOS | Allowed QOS |
| --- | --- | --- | --- |
| general | General purpose partition containing all nodes except for PI owned nodes | general | general,debug |
| preempt | Preempt partition that allows preemptable job usage of all nodes including PI owned nodes | requeue | requeue,cancel |

Partition limits are enforced using [Slurm Quality of Service (QOS)](https://slurm.schedmd.com/qos.html) for each job submitted. The default QOS for each partition is listed above in the partitions table above. The list of available partitions are shown below with their descriptions.

?> Note: Other partitions exist for PI and Department nodes where permission to use them is only given by their respective owners. More information is [below](#department-and-pi-partitions)

!> Warning: Jobs submitted to the `preempt` partition will potentially be cancelled and requeued at any time. You can specify if you want jobs to cancel and not requeue by including `#SBATCH -q cancel` to batch files or append `-q cancel` to cli commands. This only works for partition `preempt`. You will not want jobs to `requeue` (default) if the code runs for longer than a few hours and doesn't restart from where it last left off see [checkpointing](/user_guides/software_and_programming/checkpointing.md) for more information. Also for more information about `preempt` see the [Slurm Preemption](https://slurm.schedmd.com/preempt.html) page.

### Nodes

Nodes potentially available to users depending on Owner status are list below. Typically, each node has two sockets with one processor each and an equal number of cores per processor; in the table below, the CPUs column refers to physical CPUs such that 1 CPU = 1 core. In addition, please note that the maximum available memory per node for jobs is actually about 16 GB less than listed in the Memory column because some memory is reserved for system overhead (use the --mem=0 option to request all available memory on a node).

| Partition             | Nodes	| CPUs  | CPU type  |	Memory (GB) |	CPU freq (GHz)  |	GPUs  | GPU type  | Owner     |
| ---                   | ---   | ---   | ---       | ---         | ---             | ---   | ----      | ---       |
| Dell PowerEdge R6525  | 8     | 32    | EPYC-7F52	| 256         | 3.5             | 0     |           |           |
| Dell PowerEdge R7525  | 5     | 64    | EPYC-7502 | 512         | 2.5             | 2     | A100      |           |
| Dell PowerEdge R7525  | 1     | 96    | EPYC-7642 | 512         | 2.3             | 1     | A100      | sonal     |

Viewing what nodes are in each partition is done by typing the `sinfo` command.

If submitting to the `general` partition with `general` or `debug` QOS and `--gres=gpu*` is not set you will automatically be queued to the CPU only nodes. If `--gres=gpu*` is set you will automatically be queued to the GPU capable nodes. If you would like to submit to the GPU capable nodes but are not utilizing a GPU submit your job to the `preempt` partition and specify constraint `gpu` do this by either adding `#SBATCH -C gpu` to your batch file or append `-C gpu` to you cli commands. Note for this the `preempt` partition is required since this keeps it open to higher priority `general` partition GPU jobs.

?> Note: that the `*` in the above mentioned `--gres=gpu*` matches anything after `gpu` and should not be used in actual job submission.

!> Warning: jobs submitted with `general` partition and `gpu` constraint (`-C gpu` and `-p general `) will be denied since nodes need to be open to accepting GPU requiring jobs (`--gres=gpu*`).

### Quality of Service (QOS)

Below is a list of the QOS and their respective limits enforced. PreemptMode `cluster` means that the job will not be requeued or cancelled. The values below are subject to change at anytime.

| Name | PreemptMode | MaxWall | MaxTRESPerAccount | MaxJobsPerAccount | MaxSubmitPerAccount |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| general | cluster | | cpu=64,gres/gpu=2 | | |
| debug | cluster | 1 hour | cpu=32,gres/gpu=1 | 1 | 2 |
| requeue | requeue | | | | |
| cancel | cancel | | | | |


#### Descriptions

- `general` is the higher priority, default `general` partition QOS, and meant to guarantee all users at least the CPU and GPU limits from above without being requeued or cancelled (depending on preempt QOS) automatically. This is subject to the current job pressure on the general partition for whether it runs immediately or not but these jobs will preempt jobs in the `preempt` partition.
- `debug` has similar properties to `general` QOS but is used for short less than 1 hour jobs for testing your software and is subject to the CPU and GPU limits mentioned above. Note that `-q debug` or `#SBATCH -q debug` must be specified to run as debug in the general partition without this the job will always be queued in the `general` qos.
- `requeue` and `cancel` are used in the `preempt` partition and as described in their name **IF** a higher priority `debug` or `general` QOS job comes in on the `general` partition the jobs in preempt QOS will be requeued or cancelled to allow the higher priority job to run. Note that `-q cancel` or `#SBATCH -q cancel` must be specified if you desire to have jobs cancelled rather than requeued when preempted.

?> Note: Attempting to submit an invalid QOS that is not allowed on a partition or on your job specific account will cause the job to fail or be queued indefinitely. Use `sacctmgr show associations format=User,DefaultQOS,QOS,Account User=$(whoami) -p` to show your different accounts and their respective allowed QOS. The list of the Allowed QOS for each partition is shown above in the table.

### Department and PI Partitions

Nodes can be purchased by either departments or principal investigators (subject to availability and datacenter limits). The nodes purchased are directly assigned to the owners. The owners get full access to the nodes with no limits enforced. This is done by a partition being added to Slurm specifically assigned to the owned nodes. A QOS is also added which allows access to that partition. The QOS is assigned to the owners an [allocation is requested](user_guides/project_and_allocation_management/request_new_allocation.md). The owners can then [add other users](user_guides/project_and_allocation_management/adding_users.md) to the QOS so they can use the purchased nodes.

Below shows example output and commands for viewing a partition of this type and understanding how it works. Note that `{owner}` typically would be the `e-ID` of the nodes association whether PI or department. Also `{project}` is typically taken from the output of `projects` where it would be in below example `{owner}_1`

```
# sinfo -p {owner}
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
{owner}        up 30-00:00:0      1   idle node[01-05]

# scontrol show partition {owner} | grep AllowQos
   AllowGroups=ALL AllowAccounts=ALL AllowQos={owner}

# sacctmgr list associations qos={owner} format=Account,User,QOS
Account|User|QOS|
{project}|{owner}|{owner}|
{project}|user1|{owner}|
{project}|user2|{owner}|

# projects
+----+---------------+----------------------------------------+---------+--------------------+-----------------------------+----------------+-------------------+
| id |     title     |              description               |    pi   |    directories     |         resources           | slurm_accounts |     unix_group    |
+----+---------------+----------------------------------------+---------+--------------------+-----------------------------+----------------+-------------------+
| 1  |     Example   |     Example project for docs.          | {owner} | /project/{project} | {owner} (Cluster Partition) |  {project}     | project_{project} |
```

?> PI and Department partitions have to be directly specified in the job for example `{owner}` partition would be `-p {owner}` the QOS will be automatically added and is not required.

### Summary

There is a lot of important information above but some key takeaways are listed below.

- Typically only specifying partition is required if you are running a lot of jobs at which point specifying the `preempt` partition (`-p preempt`) is required once the `general` partition limits for your account and user (specified by `general` qos) is reached.
- Specifying partition is required if using Department and PI specific partitions see above.
- The only time you really need to use a specific QOS is when you are doing debugging (`-q debug`) or want `preempt` partition jobs to cancel ( `-q cancel` and `-p preempt`).
- Running gpu jobs (`--gres=gpu*`) or setting constraint to `gpu` and `preempt` partition (`-C gpu` and `-p preempt`) will automatically handle node selection and limit jobs to the GPU capable nodes.
