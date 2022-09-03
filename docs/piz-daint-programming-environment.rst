===================
CSCS SuperComputers
===================

To get your password for the account, please contact Andreas Jocksch on 
`Slack <https://cscsgpuhackathon2022.slack.com>`_.

Accessing CSCS systems
======================

Step 1: Ela
------------

In order to gain access to CSCS systems you need to first access our front-end
machine Ela which is accessible as `ela.cscs.ch`.

   .. note::

      ssh -Y hckXX@ela.cscs.ch

- If you do not have an username, you will be given a temporary username with a
  username of the form `hckXX` (for some number `XX`) and a password. The account
  is `valid until XXX`.
- If you already have an username on Piz Daint, you can use it during the
  hackathon week.

The password can be changed on Ela using the `kpasswd` command.

Step 2: Piz Daint
------------------

CSCS system is called `Piz Daint <https://www.cscs.ch/computers/piz-daint/>`_.
Piz Daint consists of the main supercomputer
and a set of front-end nodes which you would typically use for compilation,
batch job submission, file transfers and performance tools. The front end nodes
are accessible as daint.cscs.ch and you can access these as:

   .. note::

      ssh -Y daint

It is also possible to jump over ela with:

   .. note::
      ssh -Y -J hckXX@ela.cscs.ch daint.cscs.ch

Piz Daint Programming Environment
=================================

Piz Daint filesystems
---------------------

Having logged in to Daint, you will have access to two file systems:

- ``$HOME`` is a GPFS filesystem and your quota is set at 10G.
- ``$SCRATCH`` is the high-performance filesystem (Lustre) on Daint. There is no
  size quota there, but you are allowed up to 1M i-nodes (file and directory
  entries). The storage there is volatile and files will be automatically deleted
  if not accessed within a month. It is advised to run your experiments and tests
  from `$SCRATCH`. 

More information about the filesystems can be found on our 
`user portal <https://user.cscs.ch/storage/file_systems/>`_.


Cray/HPE Programming Environment
--------------------------------

The default programming environment on Piz Daint is PrgEnv-cray.
The ``module`` command can be used to change the user environment:

- To list the currently loaded module files, run:

   .. note:: 
      module list

- To list the module files that can be loaded, run:

   .. note:: 
      module avail

- To get a simple description about what a module provides, run:

   .. note:: 
      module help <modulename>

- To load another programming environment or another version of a modulefile, 
  run (for example):

   .. note:: 
      module swap PrgEnv-cray PrgEnv-gnu

      module load gcc/11.2.0

More information about the programming environment can be found on our 
`user portal <https://user.cscs.ch/computing/compilation/>`__.

CSCS Software Stack
-------------------

On top of the Cray/HPE Programming Environment, CSCS provides an HPC software
stack optimized for Daint. To load this software stack, run:

   .. note:: 
      module load daint-gpu; module avail

Available compilers
-------------------

In order to compile your code on Piz Daint, you will need to select a
programming environment for a specific compiler. Available compilers on Daint
are the Cray, Gnu, Nvidia and Intel compilers.

- To load the Programming Environment for a particular compiler, run:
    - Cray: ``module load PrgEnv-cray`` (loaded by default)
    - Gnu: ``module swap PrgEnv-cray PrgEnv-gnu``
    - Nvidia: ``module swap PrgEnv-cray PrgEnv-nvidia``
    - Intel: ``module swap PrgEnv-cray PrgEnv-intel``

Finally, code will only be compiled for GPUs if the following module is loaded:

```
module load craype-accel-nvidia60
```

NVIDIA Pascal P100
------------------

Each compute node of Piz Daint has 1 NVIDIA Pascal ``P100`` gpu:

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

For comparison, this table shows some performance metrics between 3 NVIDIA gpus:

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

TODO
----

| For OpenACC programming we suggest using the PGI compiler.
| The Cray CCE supports only up to OpenACC 2.0 and for Fortran only.
| The GCC compiler that is provided does not have support for OpenACC.
| If you want to use OpenMP 4.5 for accelerators, you should consider using the Cray compiler.


