# Software Module System

On the campus cluster, users can find and load software using the [Lmod](https://lmod.readthedocs.io/en/latest/) **module system**. Lmod dynamically changes your shell environment using **module files** to ensure the software applications and libraries you use are compatible. Module files are configuration files, written as Lua scripts, that instruct how to make an application or library available during your session. Typically, a module file contains instructions to initialize or modify environment variables, such as `PATH`.

Using a module system like Lmod is helpful because applications and libraries compiled with one compiler are not necessarily compatible with applications and libraries compiled with a different compiler, and your shell environment must be changed to accommodate these incompatibilities. **With Lmod, resetting your environment is done dynamically when you load new modules**. Loading a module will make available only compatible software for you to use. In this way, modules are organized in a hierarchy based on compilers.

### Finding software

When you log in, we automatically load a module named `siue` for you, which is actually a collection of modules. You can enter the `module list` command to view them:

```
user@login:~$ module list
Currently Loaded Modules:
  1) gcc/8.3.0   2) openblas/0.3.8   3) openmpi/4.0.2   4) pmix/3.1.3   5) siue
```

This is our default software stack and the recommended one for most users, based on the GCC 8.3.0 compiler.

The naming convention for modules is `<software_name>/<version>` (e.g., `gcc/8.3.0`).

#### Using `module avail`
To see what modules you can load into your environment, enter the command `module avail`. With `gcc/8.3.0` loaded, this will print a large number of available modules:

```
user@login:~$ module avail
-- /spack/apps/lmod/linux-centos7-x86_64/openmpi/4.0.2-ipm3dnv/openblas/0.3.8-2no6mfz/gcc/8.3.0 ---
   cantera/2.4.0-openblas    mumps/5.2.0-openblas               siesta/4.0.1-openblas
   hypre/2.18.2-openblas     netlib-scalapack/2.1.0-openblas
-------------- /spack/apps/lmod/linux-centos7-x86_64/openmpi/4.0.2-ipm3dnv/gcc/8.3.0 --------------
   charmpp/6.10.2-cuda        hdf5/1.10.6       netcdf-fortran/4.5.2    sundials/5.1.0 (D)
   charmpp/6.10.2-ucx  (D)    hmmer/3.3         parmetis/4.0.3          tau/2.29       (D)
   fftw/3.3.8-dp              matio/1.5.13      relion/3.1_beta
   fftw/3.3.8-sp       (D)    namd/2.14         scotch/6.0.8
   gromacs/2020.3             netcdf-c/4.7.3    sundials/3.1.2
------------- /spack/apps/lmod/linux-centos7-x86_64/openblas/0.3.8-2no6mfz/gcc/8.3.0 --------------
   jags/4.3.0                  r/3.5.3    r/4.0.0                     (D)
   plink2/2.00a2.3-openblas    r/3.6.3    suite-sparse/5.3.0-openblas
------------------------- /spack/apps/lmod/linux-centos7-x86_64/gcc/8.3.0 -------------------------
   adapterremoval/2.3.1                       maven/3.5.0
   anaconda3/2019.10                          megahit/1.1.4
   argtable/2-13                              mesa-glu/9.0.0
   at-spi2-atk/2.26.2                         mesa/18.3.6
   at-spi2-core/2.28.0                        meson/0.49.1
   atk/2.30.0                                 metis/5.1.0
   attr/2.4.47                                minimap2/2.14
   autoconf-archive/2019.01.06                mirdeep2/0.0.8
   autoconf/2.69                              mkfontdir/1.0.7
...
```

To unload all your loaded modules, enter the command `module purge`. Then `module list` will return No modules loaded. If you enter `module avail` again, then you will see only the `Core` modules. These are primarily compilers but can also include applications like MATLAB that are pre-built and have no dependencies:

```
user@login:~$ module avail
--------------------------- /spack/apps/lmod/linux-centos7-x86_64/Core ----------------------------
   gcc/4.9.4        intel-oneapi/2021.1-beta09        llvm/9.0.1        siue
   gcc/8.3.0 (D)    intel/18.0.4                      matlab/2019a      
   gcc/9.2.0        intel/19.0.4               (D)    pgi-nvhpc/20.7
  Where:
   D:  Default Module
Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
```

If you would like to explore the software trees available, you can load one of these core modules and then see which new ones are unlocked. For example, to see all applications built with a certain compiler, you can load that compiler module and then enter `module avail`.

#### Using module spider

If you know the name of a software package, you can use the `module spider` command to find out if it is available and how to load it.

For example, to search for Python modules:

```
user@login:~$ module spider python
------------------------------------------------------------------------
  python:
------------------------------------------------------------------------
     Versions:
        python/2.7.16
        python/3.6.8
        python/3.7.6
------------------------------------------------------------------------
  For detailed information about a specific "python" package (including
  how to load the modules) use the module's full name. Note that names
  that have a trailing (E) are extensions provided by other modules.
  For example:
     $ module spider python/3.7.6
------------------------------------------------------------------------
```

This shows that there are multiple versions of Python available. For more specific information, add the version to your command as given in the example:

```
user@login:~$ module spider python/3.7.6
------------------------------------------------------------------------
  python: python/3.7.6
------------------------------------------------------------------------
    You will need to load all module(s) on any one of the lines below
    before the "python/3.7.6" module is available to load.
      gcc/4.9.4
      gcc/8.3.0
      gcc/8.3.0  intel/18.0.4
      gcc/8.3.0  intel/19.0.4
      gcc/9.2.0
    Help:
      The Python programming language.
```

This indicates, for example, that the `gcc/8.3.0` module is required in order to load `python/3.7.6`. Alternatively, if you want to use the `gcc/9.2.0` compiler, then you need to load this module first in order to load `python/3.7.6`.

#### Using the `module keyword` command

If you do not know the exact name of a software package, you can use the module keywordcommand instead to search for modules.

For example, to search with the keyword `sam`:

```
user@login:~$ module keyword sam
------------------------------------------------------------------------
The following modules match your search criteria: "sam"
------------------------------------------------------------------------
  bamutil: bamutil/1.0.13
  cufflinks: cufflinks/2.2.1
  jags: jags/4.3.0
  libbsd: libbsd/0.9.1, libbsd/0.10.0
  pcre: pcre/8.42, pcre/8.43
  pcre2: pcre2/10.31
  perl-text-soundex: perl-text-soundex/3.05
  picard: picard/2.20.8
  samtools: samtools/1.10, samtools/18.0.4
------------------------------------------------------------------------
To learn more about a package execute:
   $ module spider Foo
where "Foo" is the name of a module.
To find detailed information about a particular package you
must specify the version if there is more than one version:
   $ module spider Foo/11.1
------------------------------------------------------------------------
```

### Loading and unloading software

Typically, loading modules is as simple as entering `module load <software_name>`. The `<software_name>` must be visible when you run `module avail`.

By entering:

```
module load <software_name>
```

Lmod will set your environment such that the software specified in `<software_name>` will be placed in your `PATH`, and then you can run the commands associated with `<software_name>`.

If there are multiple versions of `<software_name>`, you can specify a version like so:

```
module load <software_name>/<version>
```

For example, to load `gcc` version 8.3.0, enter:

```
module load gcc/8.3.0
```

If no version is specified:

```
module load gcc
```

then the default version will be loaded. The default version is indicated with a `(D)` next to it after running `module avail`.

To unload a specific module, enter:

```
module unload <software_name>
```

To see the modules that you currently have loaded, enter:

```
module list
```

To see all modules that are compatible with the currently loaded modules and available to be loaded, enter:

```
module avail
```

To unload all currently loaded modules and reset your environment, enter:

```
module purge
```

To reload the default `siue` module collection, enter:

```
module load siue
```

Enter `module help` to view more information about the available `module` commands.

Note: In order to avoid excessive network traffic on the SIUE applications server, we advise against including `module load <package>` commands directly in your `~/.bashrc` files. See this post on our user forum for some alternative methods and tips for loading modules in the most efficient way.

### Environment management

One of the best features of Lmod is that it tracks software dependencies automatically. Importantly, there are two safety features when using the module system:

1. Only one version of a module can be loaded at once.
2. Only one compiler or MPI stack can be loaded at once.

This ensures that the loaded modules are compatible with one another.

For example, let's say you want to use the `jellyfish` package compiled with `gcc/8.3.0`. You would load it like so:

```
user@login:~$ module load gcc/8.3.0 jellyfish
```

If for some reason you need to switch to the `intel` compiler set, you can use the `module swap` command to swap out the `gcc` compiler:

```
user@login:~$ module swap gcc intel
Due to MODULEPATH changes, the following have been reloaded:
  1) jellyfish/2.3.0
```

Lmod automatically changes the `jellyfish` module to one that was compiled with `intel`.

The module system can also automatically replace or deactivate modules to ensure the packages that are loaded are compatible with each other. For example, switching from `gcc/8.3.0` to `gcc/9.2.0`:

```
user@login:~$ module load gcc/9.2.0
Inactive Modules:
  1) openblas/0.3.8
Due to MODULEPATH changes, the following have been reloaded:
  1) openmpi/4.0.2     2) pmix/3.1.3
The following have been reloaded with a version change:
  1) gcc/8.3.0 => gcc/9.2.0
```

### Module settings

Loading the desired module will make some changes to your shell environment. Some common settings in module files include:

| Environment variable |	Description |
| - | - |
| `{NAME}_ROOT` |	Creates variable `{NAME}_ROOT` which points to the root directory of installation |
| `PATH` |	Adds `{NAME}_ROOT/bin` to executable search path |
| `MANPATH` |	Adds `{NAME}_ROOT/share/man` to manual search path |
| `LD_LIBRARY_PATH` |	Adds appropriate library directory to library search path |
| `PKG_CONFIG_PATH` |	Enables package to be found by `pkg-config`; useful for building other software |
| `CMAKE_PREFIX_PATH` |	Enables package build settings to be found by `cmake`; useful for building other software |

Every module is different; some will have extra environment variables, while others will have fewer.

To see what a module changes, use the `module show` command:

```
module show <software_name>
```

This will print the contents of the module file. For example:

```
user@login:~$ module show julia
-----------------------------------------------------------------------
   /spack/apps/lmod/linux-centos7-x86_64/gcc/8.3.0/julia/1.4.1.lua:
-----------------------------------------------------------------------
whatis("Name : julia")
whatis("Version : 1.4.1")
whatis("Target : x86_64")
whatis("Short description : The Julia Language: A fresh approach to technical computing")
help([[The Julia Language: A fresh approach to technical computing]])
prepend_path("PATH","/spack/apps/linux-centos7-x86_64/gcc-8.3.0/julia-1.4.1-olxtjfpuvg4iejsljm46ybjkkynflmsq/bin")
prepend_path("MANPATH","/spack/apps/linux-centos7-x86_64/gcc-8.3.0/julia-1.4.1-olxtjfpuvg4iejsljm46ybjkkynflmsq/share/man")
prepend_path("LD_LIBRARY_PATH","/spack/apps/linux-centos7-x86_64/gcc-8.3.0/julia-1.4.1-olxtjfpuvg4iejsljm46ybjkkynflmsq/lib")
prepend_path("CMAKE_PREFIX_PATH","/spack/apps/linux-centos7-x86_64/gcc-8.3.0/julia-1.4.1-olxtjfpuvg4iejsljm46ybjkkynflmsq/")
setenv("JULIA_ROOT","/spack/apps/linux-centos7-x86_64/gcc-8.3.0/julia-1.4.1-olxtjfpuvg4iejsljm46ybjkkynflmsq")
```

In this case, loading the Julia module will modify your `PATH` environment variable by prepending it with the path to the Julia binary. Additionally, it creates a new variable named `JULIA_ROOT` that you could then use if needed:

```
user@login:~$ echo $JULIA_ROOT
/spack/apps/linux-centos7-x86_64/gcc-8.3.0/julia-1.4.1-olxtjfpuvg4iejsljm46ybjkkynflmsq
```

Every module sets a `<SOFTWARE_NAME>_ROOT` variable, which is a useful shortcut pointing to the root directory of the installation.

### Additional resources

* [Lmod](https://lmod.readthedocs.io/en/latest/)
* [User Guide for Lmod](https://lmod.readthedocs.io/en/latest/010_user.html)
* [Advanced User Guide for Personal Modulefiles](https://lmod.readthedocs.io/en/latest/020_advanced.html)
