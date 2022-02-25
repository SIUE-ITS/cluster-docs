# Using GPUs

Some programs can take advantage of the unique hardware architecture in a graphics processing unit (GPU). GPUs can be used for specialized scientific computing work, including 3D modelling and machine learning. The CARC's Discovery cluster offers a few different models of GPUs for use with your jobs.

### Requesting GPU resources

On campus cluster, most GPU nodes are available on the `general` and `preempt` partition.

To request a GPU on the general partition, first add one of the following options to your Slurm job script to request the type and number of GPUs you would like to use:

```
#SBATCH --gres=gpu:<number>
```

or

```
#SBATCH --gres=gpu:<gpu_type>:<number>
```

where:

`<number>` is the number of GPUs per node requested

`<gpu_type>` is a GPU model

For Campus cluster nodes, use the chart below to determine which GPU type to specify:

| GPU type | GPU model | Max number of GPUs per node |
| --- | --- | --- |
| a100 | NVIDIA Tesla A100 | 1 or 2 (depending on node) |

For interactive jobs, use similar options with the `salloc` command:

```
salloc --gres=gpu:<gpu_type>:<number>
```

The maximum number of GPUs that can be used at one time per user, in one job or across multiple jobs, is 2. Anymore than 2 requested will have to go into the `preempt` partition queue.

### Loading GPU-related modules

GPU-enabled software often requires the CUDA Toolkit or the cuDNN library. These are available as modules and can be found by running:

```
module spider cuda
module spider cudnn
```

Or to search for modules that contain 'cud' in the name, run:

```
module spider cud
```

There are multiple versions available. To load the modules, for example, run:

```
module load gcc/8.3.0
module load cuda/10.1.243
module load cudnn/8.0.2-10.1
```

In addition, the newer NVIDIA HPC SDK with associated compilers, libraries, and related tools is available as a core module:

```
module load nvidia-hpc-sdk
```

If you require a different version of one of these modules that is not currently installed on SIUE systems, please submit a help ticket and we will install it for you.

### Compiling CUDA programs

After a `cuda` or `nvidia-hpc-sdk` module is loaded, you can then use the `nvcc` command to compile a CUDA C/C++ program:

```
nvcc program.cu -o program
```

Enter `nvcc --help` for more information on the available compiler options.

For the `nvidia-hpc-sdk` module, in addition to `nvcc`, there are NVIDIA's HPC compilers `nvc`, `nvc++`, and `nvfortran`. For example, to compile a CUDA Fortran program:

```
nvfortran program.cuf -o program
```

One advantage of these HPC compilers is that they provide GPU-acceleration of standard C++ and Fortran programs that are not explicitly written for GPUs.

Example Slurm job script
The following is an example Slurm job script for GPU jobs:

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=16GB
#SBATCH --time=1:00:00
#SBATCH --partition=gpu
#SBATCH --gres=gpu:v100:1
#SBATCH --account=<project_id>
module purge
module load nvidia-hpc-sdk
./program
```

Each line is described below:

| Command or Slurm argument	| Meaning |
| --- | --- |
| #!/bin/bash | Use Bash to execute this script |
| #SBATCH | Syntax that allows Slurm to read your requests (ignored by Bash) |
| --nodes=1 | Use 1 node |
| --ntasks=1 | Run 1 task at a time |
| --cpus-per-task=8 | Reserve 8 CPUs for your exclusive use |
| --mem=16GB | Reserve 16 GB of memory for your exclusive use |
| `--time=1:00:00` | Reserve resources described for 1 hour |
| --partition=gpu | Use Discovery's GPU partition (not required for Endeavour jobs) |
| `--gres=gpu:v100:1` | Reserve 1 K40 GPU |
| `--account=<project>` | Charge compute time to <project>. If not specified, you may use up the wrong PI's compute hours |
| module purge | Clear environment modules |
| module load nvidia-hpc-sdk | Load the nvidia-hpc-sdkcompilers and libraries environment module |
| ./program | Run program |

Make sure to adjust the resources requested based on your needs, but keep in mind that requesting fewer resources should lead to less queue time for your job.

### Additional resources

- [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit)
- [cuDNN](https://developer.nvidia.com/cudnn)
- [NVIDIA HPC SDK](https://developer.nvidia.com/hpc-sdk)
- [CUDA Zone](https://developer.nvidia.com/cuda-zone)
