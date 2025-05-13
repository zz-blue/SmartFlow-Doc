Run a case
============================

Tested Clusters
----------------------------

SmartFlow has been tested and verified on various HPC clusters in Europe and China. The following table summarizes the hardware environment and job scheduling systems used during deployment.

.. list-table:: HPC Clusters Tested with SmartFlow
   :widths: 15 25 20 20 20
   :header-rows: 1

   * - Country
     - Cluster
     - Partition
     - Scheduler
     - Network Interface 
   * - Italy
     - CINECA
     - Booster
     - Slurm
     - ib0
   * - China
     - BSCC (Beijing Super Cloud Center)
     - N32EA14P
     - Slurm
     - ib0
   * - China
     - BSCC (Beijing Super Cloud Center)
     - BSCC-A
     - Slurm
     - ib0


Running on a standalone machine
-------------------------------

To run a case on a standalone machine, we can use:

.. code-block:: sh

   cd /your/path/SmartFlow/src/smartflow
   python main.py

It should be noted that the ``main.py`` file is located in the ``/your/path/SmartFlow/src/smartflow/main.py`` . 


You may encounter an error message in your current running folder ``../../SmartFlow/examples/train_retau_05200/err`` file. Please check the error message and fix it with your own settings. Perhaps, you may see some errors about the ``wandb`` as we import ``wandb`` library. If you see ``wandb`` error, please create or login `wandb <https://wandb.ai/>`_ with your own account and add API_key to the ``main.py`` after the ``import wandb`` as follows:

.. code-block:: python

   import wandb
   wandb.login(key="your_api_key")


Running with SLURM on a CPU cluster
-----------------------------------

To run a case on a CPU cluster, we can use a SLURM script such as:

.. code-block:: sh

   #!/bin/bash
   #SBATCH --time=48:00:00
   #SBATCH --nodes=2
   #SBATCH --job-name=smartflow
   #SBATCH --account=user_account
   #SBATCH --qos=qos_name
   #SBATCH --ntasks-per-node=32
   #SBATCH --cpus-per-task=1
   #SBATCH --output=slurm-%j.out
   #SBATCH --error=slurm-%j.err

   python main.py

The script is submitted with the following command:

.. code-block:: sh

   sbatch slurm.sh

For this setting, we allocate 2 nodes with 32 tasks per node and 1 CPU per task (for a total of 64 tasks). The job is submitted to the ``qos_name`` queue under the ``user_account`` account. The job is expected to run for 48 hours. The output and error logs are saved in the ``slurm-%j.out`` and ``slurm-%j.err`` files, respectively, where ``%j`` represents the job ID.

Running with SLURM on a GPU-accelerated cluster
-----------------------------------------------

To run a case on a GPU-accelerated cluster, we can use a SLURM script such as:

.. code-block:: sh

   #!/bin/bash
   #SBATCH --time=48:00:00
   #SBATCH --nodes=2
   #SBATCH --job-name=smartflow
   #SBATCH --account=user_account
   #SBATCH --qos=qos_name
   #SBATCH --ntasks-per-node=32
   #SBATCH --cpus-per-task=8
   #SBATCH --gres=gpu:4
   #SBATCH --output=slurm-%j.out
   #SBATCH --error=slurm-%j.err

   python main.py

For this setting, we allocate 2 nodes with 32 tasks per node and 8 CPUs per task, along with 4 GPUs per node for acceleration. The job is submitted to the ``qos_name`` queue under the ``user_account`` account. The job is expected to run for 48 hours. The output and error logs are saved in the ``slurm-%j.out`` and ``slurm-%j.err`` files, respectively, where ``%j`` represents the job ID.
