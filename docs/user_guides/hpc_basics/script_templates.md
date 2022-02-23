# Script Templates
The following sections offer Slurm job script templates and descriptions for various use cases on SIUE high-performance computing (HPC) clusters.

If you're not familiar with the Slurm job scheduler or submitting jobs, please see the guide for Running Jobs.

Note: The Slurm option `--cpus-per-task refers` to physical CPUs. On SIUE compute nodes, there are typically two physical processors (sockets) per node with multiple cores per processor, such that 1 core = 1 CPU. These terms may be used interchangeably.

### Single-threaded jobs
A single-threaded (serial) job uses 1 thread on 1 compute node. This is the most basic job that can be submitted, but it does not fully utilize the compute resources available on SIUE HPC clusters.

An example job script:

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --mem=8GB
#SBATCH --time=1:00:00
#SBATCH --account=<project_id>
module purge
module load gcc/8.3.0
module load julia/1.5.2
julia script.jl
```

The `--cpus-per-task` option specifies the number of threads to use, which is 1 for a single-threaded job. Be sure to modify the `--mem` and `--time` options as needed, and add a `--partition` option if needed as well.

### Multi-threaded jobs
A multi-threaded job uses multiple threads with shared memory on 1 compute node. This is a common use case as it enables basic parallel computing.

An example job script:

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=16
#SBATCH --mem=32GB
#SBATCH --time=2:00:00
#SBATCH --account=<project_id>
module purge
module load gcc/8.3.0
module load julia/1.5.2
julia --threads $SLURM_CPUS_PER_TASK script.jl
```
