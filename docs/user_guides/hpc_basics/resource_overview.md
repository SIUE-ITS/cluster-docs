# Campus Cluster Resource Overview
SIUE's general use high-performance computing cluster, CC, has over 12 compute nodes available for users to run their jobs on.

For general SIUE system specifications, see our [Getting Started](user_guides/hpc_basics/getting_started.md) page.

### Partitions and compute nodes
There are a few Slurm partitions available on CC, each with a separate job queue. These are general-use partitions available to all researchers. The table below describes the intended purpose for each partition.

| Partition | Description |
| --- | --- |
| CPU | General purpose CPU only jobs with no time limit enforced |
| GPU | GPU only jobs with 1-2 Nvidia A100s per node with no time limit enforced |

Each partition has a different mix of compute nodes. The table below describes the available nodes on each of the partitions. Typically, each node has two sockets with one processor each and an equal number of cores per processor; in the table below, the CPUs column refers to physical CPUs such that 1 CPU = 1 core. In addition, please note that the maximum available memory per node for jobs is actually a few GB less than listed in the Memory column because some memory is reserved for system overhead (use the --mem=0 option to request all available memory on a node).

| Partition	| Nodes	| CPUs |	Memory (GB)	| CPU type |	CPU freq |	GPU type |
| --- | --- |--- | --- | --- | --- | --- |
| CPU |	8	| 32	| 256	| EPYC-7F52	| 3.5 GHz | None |
| GPU | 4 | 64 | 512 | EPYC-7502 | 2.5 GHz | A100 |
