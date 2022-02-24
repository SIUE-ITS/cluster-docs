# Script Templates
The following sections offer Slurm job script templates and descriptions for various use cases on SIUE high-performance computing (HPC) clusters.

If you're not familiar with the Slurm job scheduler or submitting jobs, please see the guide for [Running Jobs](user_guides/hpc_basics/running_jobs.md).

?> Note: The Slurm option `--cpus-per-task` refers to physical CPUs. On SIUE compute nodes, there are typically two physical processors (sockets) per node with multiple cores per processor, such that 1 core = 1 CPU. These terms may be used interchangeably.

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
#SBATCH --account=<project>
module load julia
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
#SBATCH --account=<project>
module load julia
julia --threads $SLURM_CPUS_PER_TASK script.jl
```

The `--cpus-per-task` option limits the number of threads that can be used. There is 1 thread per CPU, so >1 CPU is needed for a multi-threaded job. Make sure to modify the `--cpus-per-task`, `--mem`, and `--time` options as needed, and add a `--partition` option if needed as well. The number of CPUs and amount of memory that can be used varies across compute nodes. For more details, see the [resource overview](user_guides/hpc_basics/resource_overview.md).

Please note that you may have to modify your scripts and programs to explicitly use multiple threads, depending on the application or programming language you are using.

Some compiled programming languages like C, C++, and Fortran use [OpenMP](https://www.openmp.org/) for multithreading. In these cases, you should compile your programs with an `openmp` flag and explicitly set the environment variable `OMP_NUM_THREADS` (number of threads to parallelize over) in your job scripts. The `OMP_NUM_THREADS` count should equal the requested `--cpus-per-task` option in the job script. You can use the Slurm-provided environment variable `SLURM_CPUS_PER_TASK` to set `OMP_NUM_THREADS`.

An example job script using OpenMP:

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=16
#SBATCH --mem=32GB
#SBATCH --time=1:00:00
#SBATCH --account=<project>
ulimit -s unlimited
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
./omp_program
```

### Single-threaded MPI jobs

The Message Passing Interface (MPI) is a message-passing standard used in parallel programming, primarily when using multiple compute nodes with distributed memory. For more information, see the [Using MPI user guide](user_guides/software_and_programming/using_mpi.md).

An example job script for a single-threaded MPI program:

```
#!/bin/bash
#SBATCH --nodes=6
#SBATCH --ntasks=144
#SBATCH --cpus-per-task=1
#SBATCH --mem-per-cpu=3GB
#SBATCH --time=24:00:00
#SBATCH --exclusive
#SBATCH --account=<project>
ulimit -s unlimited
srun --mpi=pmix_v2 -n $SLURM_NTASKS ./mpi_program
```

The `--nodes` option specifies how many nodes to use, and the `--ntasks` option specifies the number of tasks to run (MPI ranks). The `--cpus-per-task` option limits the number of threads that can be used per task. There is 1 thread per CPU, so only 1 CPU per task is needed for a single-threaded MPI job. Make sure to modify the `--mem-per-cpu` and `--time` options as needed, and add `--ntasks-per-node` or `--partition` options if needed as well.

For best performance, MPI jobs should use entire nodes, ideally of the same type. The job options used should reflect the specific node characteristics (e.g., matching the total number of tasks to the total number of CPUs across nodes). For more information, see the Using MPI user guide.

### Multi-threaded MPI jobs

Multi-threaded MPI jobs run multi-threaded tasks on multiple compute nodes, typically hybrid OpenMP/MPI jobs. For optimal performance, there are additional arguments that should be passed to the program. If using OpenMP for threading, for example, the environment variable `OMP_NUM_THREADS` should be set, which specifies the number of threads to parallelize over. The `OMP_NUM_THREADS` count should equal the requested `--cpus-per-task` option in the job script. You can use the Slurm-provided environment variable `SLURM_CPUS_PER_TASK` to set `OMP_NUM_THREADS`.

An example job script for a multi-threaded MPI program:

```
#!/bin/bash
#SBATCH --nodes=10
#SBATCH --ntasks=120
#SBATCH --cpus-per-task=2
#SBATCH --mem-per-cpu=2GB
#SBATCH --time=24:00:00
#SBATCH --constraint=xeon-4116
#SBATCH --exclusive
#SBATCH --account=<project>
ulimit -s unlimited
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
srun --mpi=pmix_v2 --cpu-bind=ldoms -n $SLURM_NTASKS ./mpi_omp_program
```

The `--nodes` option specifies how many nodes to use, and the `--ntasks` option specifies the number of tasks to run (MPI ranks). The `--cpus-per-task` option limits the number of threads that can be used per task. There is 1 thread per CPU, so >1 CPU per task is needed for a multi-threaded MPI program. Make sure to modify the `--mem-per-cpu` and `--time` options as needed, and add `--ntasks-per-node` or `--partition` options if needed as well.

For best performance, MPI jobs should use entire nodes, ideally of the same type. The job options used should reflect the specific node characteristics (e.g., setting the number of threads/CPUs per task to stay within NUMA domains). For more information, see the Using MPI user guide.

### GPU jobs

Some programs can take advantage of the unique hardware architecture in a graphics processing unit (GPU). GPUs can be used for specialized scientific computing work, including 3D modelling and machine learning. For more information, see the [Using GPUs user guide](user_guides/software_and_programming/using_gpus.md).

An example job script for GPUs:

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=16GB
#SBATCH --time=1:00:00
#SBATCH --partition=gpu
#SBATCH --gres=gpu:a100:1
#SBATCH --account=<project>
./program
```

### Job arrays

Job arrays allow you to use a single job script to launch many similar jobs. Common use cases include:

- Varying model parameters (e.g., running simulations with different conditions)
- Processing different input files (e.g., running data analysis pipelines with different datasets)

An example job script for a job array:

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --mem-per-cpu=2GB
#SBATCH --time=1:00:00
#SBATCH --account=<project>
#SBATCH --array=1-10
./my_program --input=input_${SLURM_ARRAY_TASK_ID} --output=output_${SLURM_ARRAY_TASK_ID}
```

This example uses a program `my_program` that takes the options `--input-file` and `--output-file` to specify the paths to the input and output files, assuming the input files are named similar to input_1, input_2, etc. Job scripts for job arrays typically make use of the environment variable `SLURM_ARRAY_TASK_ID` to iterate over parameters or input files in some manner. This example job script would launch 10 jobs with the same `sbatch` options but using the different input files and creating different output files, based on the `SLURM_ARRAY_TASK_ID` index (in this example, 1-10). Array job 1 would use input_1 and create output_1, array job 2 would use input_2 and create output_2, etc. This is one possible setup for an array job, but alternative setups are also possible.

?> Please note that the job array size should match the input size (i.e., the number of simulations to run or the number of datasets to process). Make sure that the resources you request are sufficient for one individual job (not the entire array as a whole).
