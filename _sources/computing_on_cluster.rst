Computing on Cluster
====================

1. Login nodes vs. compute nodes
--------------------------------
When you login to a cluster using ``ssh``, you are logging into a login node. The login node is a shared resource that is used by all users to access the cluster. The login node is not meant for running computationally intensive jobs. Instead, you should run your jobs on the compute nodes. The compute nodes are the nodes that are dedicated to running your jobs. The login node is used to allocate resources on the compute nodes, submit jobs to the compute nodes, and monitor the status of your jobs.

2. Slurm
--------
The compute nodes are managed by a job scheduler called Slurm. Slurm is used to allocate resources on the compute nodes, submit jobs to the compute nodes, and monitor the status of your jobs. 
Slurm groups compute nodes into partitions. Each partition has a set of compute nodes that are dedicated to running a specific type of job. For example, a cluster may have a partition for running jobs that require a lot of memory, and another partition for running jobs that require a GPU (Graphics Processing Unit). When you submit a job to Slurm, you specify the partition that you want to run the job on.

There are two ways to run a job via Slurm. You can either run an interactive job or a batch job. An interactive job is a job that you run interactively on the compute nodes. A batch job is a job that you submit to Slurm and it runs in the background on the compute nodes.
Interactive jobs are useful for developing and debugging your code. You can run your code interactively on the compute nodes and see the output in real-time. Batch jobs are useful for running computationally intensive jobs that take a long time to complete. You submit your job to Slurm and it runs in the background on the compute nodes.

Interactive jobs
+++++++++++++++++++++
For example, you can use ``salloc -p dinglab -n 1 --time=8:00:00 -c 4 srun --pty bash -i`` to run an interactive job on a compute node. This command will allocate one node from the ``dinglab`` partition for 8 hours with 4 CPUs and start an interactive shell on the compute node. You can then run your code interactively on the compute node. You can change the partition, the number of nodes, the time limit, and the number of CPUs to suit your needs. To see other options that you can use with ``salloc``, you can run ``man salloc``.

Submit batch jobs
+++++++++++++++++++++
Let's say you have the following bash script called ``myscript.sh`` that you want to run on the compute nodes:

.. code-block:: bash

   #!/bin/bash

   echo "Hello, World!"
   sleep 5
   
To submit this script as a batch job to Slurm, you can use the following command:
``sbatch -p dinglab -n 1 --time=05:00 -c 1 myscript.sh``. This command will submit the script as a batch job to the ``dinglab`` partition with 1 node, a time limit of 5 mins, and 1 CPU. Of course, this job will finish in 5 seconds and when it is done, slurm will automatically release the resources back to the cluster.
Note that the first line of the script must be ``#!/bin/bash``. This line tells the operating system that the script should be run using the bash shell.

It is not very convenient to have to type the partition, the number of nodes, the time limit, and the number of CPUs in the command line every time you want to submit this job. Instead, you can specify these options inside the script itself. You can add the following lines to the script:

.. code-block:: bash

    #!/bin/bash
    #SBATCH --job-name=any_name_to_choose
    #SBATCH --partition=dinglab
    #SBATCH --time=05:00
    #SBATCH --nodes=1
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=1
    #SBATCH --open-mode=trucate
    #SBATCH --output=./log_%A.out

    echo "Hello, World!"
    sleep 5

Then you can submit the script as a batch job to Slurm using ``sbatch myscript.sh``. ``sbatch`` will read the options from the script and submit the job to the specified partition with the specified number of nodes, time limit, and number of CPUs. We also added the ``--output`` option to specify the output file. The ``%A`` in the output file name will be replaced by the job ID. The output file will contain the output of the script which is ``Hello, World!`` in this case.

Now what if you want to run the following Python script called ``myscript.py`` on the compute nodes:

.. code-block:: python

    import time
    print("Hello, World!")    
    time.sleep(5)

You have two options. You can either run the Python script inside a bash script, or you can run the Python script directly using the Python interpreter. To run the Python script inside a bash script, you can create a bash script called ``myscript.sh`` with the following lines:

.. code-block:: bash

    #!/bin/bash
    #SBATCH --job-name=any_name_to_choose
    #SBATCH --partition=dinglab
    #SBATCH --time=05:00
    #SBATCH --nodes=1
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=1
    #SBATCH --open-mode=trucate
    #SBATCH --output=./log_%A.out

    ## your need to activate the conda environment first
    conda activate lab
    python myscript.py

You can then submit the bash script as a batch job to Slurm using ``sbatch myscript.sh``.

Alternatively, you can run the Python script directly using the Python interpreter. In order to do it, you need to change the python scripts as follows:

.. code-block:: python

    #!/cluster/tufts/dinglab/shared_apps/miniconda3/envs/lab/bin/python
    #SBATCH --job-name=any_name_to_choose
    #SBATCH --partition=dinglab
    #SBATCH --time=05:00
    #SBATCH --nodes=1
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=1
    #SBATCH --open-mode=trucate
    #SBATCH --output=./log_%A.out

    import time
    print("Hello, World!")    
    time.sleep(5)

Then you can submit the Python script as a batch job to Slurm using ``sbatch myscript.py``. Here the first line of the script is ``#!/cluster/tufts/dinglab/shared_apps/miniconda3/envs/lab/bin/python``. This line tells the operating system to run the script using the Python interpreter located at ``/cluster/tufts/dinglab/shared_apps/miniconda3/envs/lab/bin/python``. You can find the path to the Python interpreter by running ``which python`` in the terminal.



Other useful commands
+++++++++++++++++++++++++++++

- ``sinfo``: Display information about the compute nodes. If you run ``sinfo`` without any arguments, it will display information about all the partitions on the cluster that you can submit jobs to. It also displays information about the time limit, the number of nodes and the list of nodes in each partition. If you run ``sinfo -o %P,%N,%G,%C,%m``, it will display information about the partitions, the nodes, the number of GPUs, the number of CPUs and the memory of each node.

- ``squeue``: Display information about the jobs that are running or waiting to run on the compute nodes. If you run ``squeue`` without any arguments, it will display information about all the jobs that are running or waiting to run on the compute nodes. If you run ``squeue -u username``, it will display information about the jobs that are running or waiting to run on the compute nodes for the specified username.

- ``scancel job_id``: Cancel a job that is running or waiting to run on the compute nodes. You can find the job ID by running ``squeue``.

- ``scontrol show job job_id``: Display detailed information about a job that is running or waiting to run on the compute nodes. You can find the job ID by running ``squeue``.

