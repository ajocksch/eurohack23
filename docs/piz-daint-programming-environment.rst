==================
CSCS Supercomputer
==================

To get your password for the account, please contact `Andreas Jocksch
<https://cscshackathon.slack.com/team/U05QMMKLZ0W>`_ on Slack. 

Accessing Piz Daint
===================

Step 1: Ela
------------

In order to gain access to CSCS systems you need to first access our front-end
machine Ela which is accessible as `ela.cscs.ch`.

   .. code-block::

      ssh -Y hckXX@ela.cscs.ch

- If you do not have an username, you will be given a temporary username with a
  username of the form `hckXX` (for some number `XX`) and a password.
  The password can be changed on Ela using the `kpasswd` command.

Step 2: Piz Daint
------------------

CSCS system is called `Piz Daint <https://www.cscs.ch/computers/piz-daint/>`_.
Piz Daint consists of the main supercomputer
and a set of front-end nodes which you would typically use for compilation,
batch job submission, file transfers and performance tools. The front end nodes
are accessible as daint.cscs.ch and you can access these as:

   .. code-block::

      ssh -Y daint

It is also possible to jump over ela with:

   .. code-block::

      ssh -Y -J hckXX@ela.cscs.ch daint.cscs.ch

Piz Daint filesystems
=====================

Having logged in to Daint, you will have access to two file systems:

- ``$HOME`` is a GPFS filesystem and your quota is set at 10G.
- ``$SCRATCH`` is the high-performance filesystem (Lustre) on Daint. There is no
  size quota there, but you are allowed up to 1M i-nodes (file and directory
  entries). The storage there is volatile and files will be automatically deleted
  if not accessed within a month. It is advised to run your experiments and tests
  from `$SCRATCH`. 

More information about the filesystems can be found on our 
`user portal <https://user.cscs.ch/storage/file_systems/>`_.

Piz Daint Programming Environment
=================================

Cray/HPE Programming Environment
--------------------------------

The default programming environment on Piz Daint is PrgEnv-cray.
The ``module`` command can be used to change the user environment:

- To list the currently loaded module files, run:

   .. code-block:: 

      module list

- To list the module files that can be loaded, run:

   .. code-block:: 

      module avail

- To get a simple description about what a module provides, run:

   .. code-block:: 

      module help <modulename>

- To load another programming environment or another version of a modulefile, 
  run (for example):

   .. code-block:: 

      module swap PrgEnv-cray PrgEnv-gnu
      module load gcc/11.2.0

More information about the programming environment can be found on our 
`user portal <https://user.cscs.ch/computing/compilation/>`__.

Available compilers
```````````````````

In order to compile your code on Piz Daint, you will need to select a
programming environment for a specific compiler. Available compilers on Daint
are the Cray, Gnu, Nvidia and Intel compilers.

- To load the Programming Environment for a particular compiler, run:
    - Cray: ``module load PrgEnv-cray`` (loaded by default)
    - Gnu: ``module swap PrgEnv-cray PrgEnv-gnu``
    - Nvidia: ``module swap PrgEnv-cray PrgEnv-nvidia``
    - Intel: ``module swap PrgEnv-cray PrgEnv-intel``

The Cray programming environment provides compilation wrapper scripts that
will automatically set cpu arch flags (Intel Haswell) and add header files and
libraries that you have loaded with the module command:

- The wrapper command for Fortran codes is ``ftn``
- The wrapper command for C++ codes is ``CC``
- The wrapper command for C codes is ``cc``

To get the version of the compiler used by the wrapper, run
``CC --version -craype-verbose``.

   .. warning::
      The wrappers (ftn, cc or CC) are mandatory to compile MPI codes: there are no `mpicc` and the likes.

- To compile a single file (fortran, c or c++) MPI code, run:

   .. code-block:: 

      ftn -O2 mpi.f90 -o myexe
      cc -O2 mpi.c -o myexe
      CC -O2 mpi.cpp -o myexe

- For more information about the compilers, please check our user portal:

   .. note:: 

      - PrgEnv-Cray: https://user.cscs.ch/computing/compilation/cray
      - PrgEnv-gnu: https://user.cscs.ch/computing/compilation/gnu/
      - PrgEnv-nvidia: https://user.cscs.ch/computing/compilation/nvidia/
      - PrgEnv-intel: https://user.cscs.ch/computing/compilation/intel/

CSCS Software Stack
-------------------

On top of the Cray/HPE Programming Environment, CSCS provides an HPC software
stack optimized for Daint. To load this software stack, run:

   .. code-block:: 

      module load daint-gpu
      module avail

Building your code with EasyBuild or Spack
------------------------------------------

It is possible to use either `EasyBuild
<https://user.cscs.ch/computing/compilation/easybuild/>`__ or `Spack
<https://user.cscs.ch/computing/compilation/spack/>`__ to build more codes and
dependencies.

Piz Daint GPU specs
===================

Each compute node of Piz Daint has 1 NVIDIA Pascal ``P100`` gpu:

- To compile cuda codes on Piz Daint using the default versions, run:

   .. code-block:: 

      module load craype-accel-nvidia60
      nvcc --version

It will load the default nvcc compiler (**cudatoolkit/11.0.2**) and default Cray
library for gpu (**cray-libsci_acc/20.10.1**). If you need a more recent version,
use `/opt/nvidia/hpc_sdk/Linux_x86_64/` or `/apps/daint/UES/hackaton/software/CUDAcore/11.8.0/` 

If you need another version, please contact ``#cluster-support-pizdaint`` on Slack.

NVIDIA Pascal P100
------------------

For comparison, this table shows some performance metrics between the P100 on
Piz Daint compute nodes and 2 more recent NVIDIA gpus:

.. table::
   :align: center
   :class: tiny

   ================  ========  ========  ========
   \                   *P100*    *V100*    *A100*
   ----------------  --------  --------  --------
   *Clock rate*      1.33 GHz  1.38 GHz  1.41 GHz
   *Peak perf*       4.8 TF/s  7.1 TF/s  9.7 TF/s
   *70% of peak*     3.3 TF/s  4.9 TF/s  6.8 TF/s
   *GPU Memory*         16 GB     32 GB     40 GB
   *nvcc -arch=sm_*        60        70        80
   ================  ========  ========  ========

and some details about the GPU configuration:

.. table:: 
   :align: center
   :class: tiny

   ================  ========  ======  ======  ==========
   1 compute node    *thread*  *warp*   *sm*    *device* 
   = 1 P100 gpu                                                  
   ----------------  --------  ------  ------  ----------
   *threads*                1      32    2048      114688
   *warps*                  x       1      64        3584
   *sms*                    x       x       1          56
   *GPU*                    x       x       x   P100-PCIE
   ================  ========  ======  ======  ==========


Piz Daint Job Scheduler
=======================

The Job Scheduler on Piz Daint is Slurm.
In order to run your code you will need to get 1 or more compute nodes from
the batch system.

- For basic development, an interactive session can be started on the login
  nodes of Piz Daint using ``salloc``. When you have been granted a set of
  nodes/gpus, you then use the ``srun`` command to launch jobs on the compute
  nodes,
- For non interactive jobs, you can use the ``sbatch`` command. The mentors
  will help you generate batch submission scripts.

The flags that you pass to ``srun`` differ depending upon whether you are
running MPI or OpenMP parallel applications.

When you have finished your session, you should exit the by typing `exit` so
that your processors are returned back to the pool.

Before the hackathon, you will have to compete with other jobs running on the
system in the normal queue:

   .. code-block:: 

      salloc -Ahck -Cgpu -N 1 --time=01:00:00

You can also use the ``debug`` queue for quicker response times, but your jobs
must be limited to a single node only and have a time limit of 30 minutes.

   .. code-block:: 

      salloc -Ahck -Cgpu -N 1 --partition=debug

OpenACC
=======

- The Cray CCE compiler supports only supports Fortran (up to OpenACC 2.0).
- The Nvidia compiler supports OpenACC.
- The GCC compiler available on Piz Daint do not have support for OpenACC.
- If you want to use OpenMP 4.5 for accelerators, you should consider using the Cray compiler.

