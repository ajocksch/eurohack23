=================
Performance tools
=================

This section gives an overview of some of the gpu performance analysis tools
available on Piz Daint. Feel free to contact us on ``#profiler_questions``
or ``#cluster_support_pizdaint`` in Slack for more information.

NVIDIA Nsight Systems
=====================

- `Nsight™ Systems <https://developer.nvidia.com/nsight-systems>`__ is NVIDIA®
  system-wide performance analysis tool. Several versions are installed on Piz
  Daint. It is also recommended to download and install it on your laptop.

  .. warning::

     `Nsight™ Compute <https://developer.nvidia.com/nsight-compute>`__ is
     `not supported <https://docs.nvidia.com/cuda/profiler-users-guide/index.html#migrating-to-nsight-tools>`__  
     on Piz Daint with NVIDIA P100 gpus.

Load nsys on Piz Daint
----------------------

The latest version of **Nsight Systems** available on Piz Daint is `2023.3.1`:

   .. code-block::

      /apps/daint/UES/hackaton/software/nvidia-nsight-systems/2023.3.1/bin/nsys --version
      NVIDIA Nsight Systems version 2023.3.1.92-233133147223v0

Run a job with nsys on Piz Daint
--------------------------------

An example jobscript is available in `/scratch/snx3000/piccinal/reframe/stage/daint/gpu/PrgEnv-gnu/nsys/`.
For example, add to your jobscript:

   .. code-block::

      nsys='/apps/daint/UES/hackaton/software/nvidia-nsight-systems/2023.3.1/bin/nsys'
      
      nsys_opt1='profile --force-overwrite=true'
      nsys_opt2='-o %h.%q{SLURM_NODEID}.%q{SLURM_PROCID}.nsys-rep'
      nsys_opt3='--trace=cuda,mpi,nvtx --mpi-impl=mpich'
      nsys_opt4='--stats=true --delay=2'
      
      srun nsys $nsys_opt1 $nsys_opt2 $nsys_opt3 $nsys_opt4 ./myexe -- myexe_args

Get more informations about the tool with:

    .. note::

      ``nsys profile --help``

You may also want to track the GPU memory usage with the
``'--cuda-memory-usage=true'`` flag. This feature may cause significant runtime
overhead. You can also deactivate cpu reporting with ``'--sample=none'``.

Open a report file with nsys on your laptop
-------------------------------------------

A succesfull completed job on Piz Daint will create 1 or more ***.nsys-rep** report
file(s). Transfer the files to your laptop with scp and open them with the
installed nsys client.

.. ![nsys on Piz Daint](img/nsys.png)

.. image:: _static/img/nsys.png
  :align: center
  :width: 600
  :alt: nsys

NVIDIA nvprof
=============

- `nvprof <https://docs.nvidia.com/cuda/profiler-users-guide/index.html#profiling-modes>`__
  and `NVVP <https://developer.nvidia.com/nvidia-visual-profiler>`__ are
  NVIDIA® previous generation profiler and gui.

  .. warning::

     It is recommended to use `Nsight Systems` (nsys).
     If you need help with nvprof, contact us please.
