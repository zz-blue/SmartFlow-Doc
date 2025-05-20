Installation
============================

The following components are required to use the SmartFlow framework:

1. :ref:`SmartSim and SmartRedis <smartsim-and-smartredis>`
2. :ref:`Stable-Baselines3 <stablebaselines3>`
3. :ref:`SmartFlow <smartflow>`
4. :ref:`SmartRedis-MPI <smartredismpi>`
5. :ref:`CFD Solver <cfd-solver>`

For comprehensive installation instructions, please refer to the official documentation for each component:

- `SmartSim and SmartRedis <https://www.craylabs.org/docs/installation_instructions/basic.html>`_
- `SmartRedis-MPI <https://github.com/soaringxmc/smartredis-mpi>`_
- `Stable-Baselines3 <https://stable-baselines3.readthedocs.io/en/master/guide/install.html>`_

If you plan to contribute advanced features to the framework, we recommend thoroughly reviewing both the
`SmartSim Documentation <https://www.craylabs.org/docs/overview.html>`_ and
`Stable-Baselines3 Documentation <https://stable-baselines3.readthedocs.io/en/master/>`_ documentation.

Below is a quick installation guide for all components. Note that Python 3.9–3.11 is required for compatibility.
We recommend starting with a fresh virtual environment if your system has already installed Python packages.:

.. code-block:: sh

   python -m venv /path/to/smartflow/environment
   source /path/to/smartflow/environment/bin/activate

Also you can use anaconda to create and manage your virtual environment:

.. code-block:: sh

   # Create a new conda environment named "smartflow" with Python 3.10
   conda create -n smartflow python=3.10
   conda activate smartflow


.. note::

   SmartFlow fully supports **OpenMPI** by default. Using other MPI implementations may require extra setup or tuning.


.. _smartsim-and-smartredis:

1. SmartSim and SmartRedis
----------------------------

SmartSim provides the infrastructure for launching and managing simulations, while SmartRedis provides client
interfaces for interacting with the in-memory Redis database.

**Python Installation**

.. code-block:: sh

   # Install SmartSim
   pip install smartsim
   # Build SmartSim with CPU support and Dragon
   smart build --device cpu --dragon
   # If you are using HPC, you shall load cmake and pay attention to cmake verion. 
   # Install SmartRedis Python client
   pip install smartredis

.. note::

   The ``--dragon`` option is not required essentially. The following command also works:

   .. code-block:: sh

      smart build --device cpu


**C, C++, and Fortran Client Libraries**

For compiled language applications, the SmartRedis client libraries need to be built from source.
We currently use version 0.5.3 for compatibility.
   

**Using GCC Compilers**

.. code-block:: sh

   git clone https://github.com/CrayLabs/SmartRedis --depth=1 --branch=v0.5.3 smartredis
   cd smartredis
   make lib-with-fortran CC=gcc CXX=g++ FC=gfortran

After compilation, add the library path to your environment:

.. code-block:: sh

   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/smartredis/lib
   # Add this to your .bashrc or .bash_profile for persistence

**Using NVIDIA Compilers (for GPU-enabled applications)**

.. code-block:: sh

   cd smartredis
   make lib-with-fortran CC=nvc CXX=nvc++ FC=nvfortran

**Installation Notes and Verification**

The CFD solver, SmartRedis, and SmartRedis-MPI **must** be compiled with the same compiler to ensure proper linking.

The command ``smart build --device cpu --dragon`` not only builds SmartSim with CPU and Dragon support,
but also installs and configures ``RedisAI``, enabling machine learning model execution within Redis.

After the installation, you can verify the setup using the following command:

.. code-block:: sh

   smart info

If the installation is successful, you should see output similar to the following:

.. code-block:: text

   Smart Python Packages:
   ╒════════════╤═══════════╕
   │ Name       │ Version   │
   ╞════════════╪═══════════╡
   │ SmartSim   │ 0.7.0     │
   │ SmartRedis │ 0.5.3     │
   ╘════════════╧═══════════╛

   Orchestrator Configuration:
   ╒═══════════╤═════════════════════════════════════════════════════════════════════════════════════╕
   │ Installed │ REDIS                                                                               │
   │ Location  │ /smartsod2d/lib/python3.10/site-packages/smartsim/_core/bin/redis-cli │
   ╘═══════════╧═════════════════════════════════════════════════════════════════════════════════════╛

   Redis AI Configuration:
   ╒══════════╤══════════════════════════════════════════════════════════════════════════════════════╕
   │ Status   │ Installed                                                                            │
   │ Location │ /smartsod2d/lib/python3.10/site-packages/smartsim/_core/lib/redisai.so │
   ╘══════════╧══════════════════════════════════════════════════════════════════════════════════════╛

   Machine Learning Backends:
   ╒════════════╤═════════════════════╤══════════════════╕
   │ Name       │ Backend Available   │ Python Package   │
   ╞════════════╪═════════════════════╪══════════════════╡
   │ Tensorflow │ True                │ 2.15.0           │
   │ Torch      │ True                │ 2.6.0            │
   │ ONNX       │ False               │ Not Installed    │
   ╘════════════╧═════════════════════╧══════════════════╛

.. _stablebaselines3:

2. Stable-Baselines3
----------------------------

Stable-Baselines3 is a Python library that provides implementations of reinforcement learning algorithms.
It can be installed with the following command:

.. code-block:: sh

   pip install stable-baselines3[extra]

.. _smartflow:

3. SmartFlow
----------------------------

We recommand to use `SmartFlow-v1.0.1 <https://github.com/soaringxmc/SmartFlow/releases/tag/v1.0.1>`_ as a stable version.

To install SmartFlow, clone the repository and install the required packages:

.. code-block:: sh

   # Directory where you want to clone the repository
   cd /your/local/dir/code
   # Clone the SmartFlow repository
   git clone https://github.com/soaringxmc/SmartFlow.git --branch=v1.0.1

SmartFlow is installed with the following commands:

.. code-block:: sh

   cd SmartFlow
   pip install -e .

This will mark the current package as editable, so it can be modified and the changes will be automatically
available to the Python environment.

**Scripts Installation**

The scripts in SmartFlow rely on additional Python packages that may not be listed in the default installation.
In particular, make sure the following libraries are installed:

- ``omegaconf``
- ``wandb``

You can install them with:

.. code-block:: sh

   pip install omegaconf wandb


.. _smartredismpi:

4. SmartRedis-MPI
----------------------------

Before installing the CFD solver, build the SmartRedis-MPI library that will be linked by the MPI-based parallel CFD solver:

.. code-block:: sh

   git clone https://github.com/soaringxmc/smartredis-mpi.git
   cd smartredis-mpi

Edit the ``Makefile`` to set the correct settings and paths to your SmartRedis installation:
The ``Makefile`` is located in the folder of the ``smartredis-mpi`` repository



.. code-block:: sh

   vim Makefile
   # Adjust the paths in the Makefile
   # in line 2 set the compiler
   COMPILER =GNU 

   # In line 4 set set the precision 
   SINGLE_PRECISION = 0 

   # In line 6 set the smartredis installation path, it should be the same as the one you set in the SmartRedis installation (step 1) or the one you set in the SmartRedis-MPI installation (step 4)
   SMARTREDIS INSTALL DIR = /your/local/dir/code/smartredis/instal1


.. code-block:: sh
   
   # after all your settings are done, run the following command to build the library
   make clean
   make


Add the library path to your environment:

.. code-block:: sh

   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/your/dir/smartredis-mpi/build/lib
   # Add this to your .bashrc or .bash_profile for persistence


.. _cfd-solver:

5. CFD Solver
----------------------------

The advantage of SmartFlow is that it can be easily integrated with any CFD solver. Only several lines of code
need to be added to the CFD solver to enable communication with the SmartFlow framework.

As an example, we only added five lines of code to the `CaLES <https://github.com/CaNS-World/CaLES>`_ solver
to enable its coupling with the SmartFlow framework.

To use SmartFlow v1.0.1 with CaLES, please ensure you are using the compatible version: `CaLES-v2.0.0-smartflow <https://github.com/CaNS-World/CaLES/releases/tag/v2.0.0-smartflow>`_


.. code-block:: sh

   # Clone the CaLES repository smartflow branch
   cd /your/local/dir/code
   git clone -b smartflow https://github.com/CaNS-World/CaLES.git

If you want to use CaLES as your CFD solver or simply test the workflow of the SmartFlow framework,
please refer to the `CaLES <https://github.com/CaNS-World/CaLES>`_ for installation instructions. After installing the CaLES solver, you shall update the dependencies as follows:

.. code-block:: sh

   # your CaLES installation directory
   cd /your/local/dir/code/CaLES
   # update the submodules
   git submodule update --init --recursive

After that, you can build the solver with the following command:

.. code-block:: sh

   # in your current working directory, clean all cache
   make allclean
   # build the solver
   make libs && make -j 


**Compilation Options**

- **Branch Selection**: Make sure to use the ``smartflow branch``, not the main branch.
- **Build Configuration**: Ensure that the parameter ``PENCIL_AXIS`` is set to **3** in the ``build.conf`` file.

We suggest presenting the CFD solvers coupled to SmartFlow in the following table:

.. list-table:: CFD Solvers Coupled with SmartFlow
   :widths: 25 25 25 25
   :header-rows: 1

   * - Solver
     - Coupling Status
     - Device Support
     - Numerical Method
   * - CaLES
     - Coupled
     - CPU/GPU
     - Finite Difference
   * - SOD2D
     - Coupled
     - CPU/GPU
     - Spectral Element
   * - FLEXI
     - In progress
     - CPU
     - Discontinuous Galerkin
   * - HORSES3D
     - In progress
     - CPU
     - Discontinuous Galerkin
