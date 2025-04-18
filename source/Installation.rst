Installation
============================

The following components are required to use the SmartFlow framework:

1. SmartSim and SmartRedis  
2. Stable-Baselines3  
3. SmartFlow  
4. SmartRedis-MPI  
5. CFD solver  

For comprehensive installation instructions, please refer to the official documentation for each component:

- `SmartSim and SmartRedis <https://www.craylabs.org/docs/installation_instructions/basic.html>`_
- `SmartRedis-MPI <https://github.com/soaringxmc/smartredis-mpi>`_
- `Stable-Baselines3 <https://stable-baselines3.readthedocs.io/en/master/guide/install.html>`_

If you plan to contribute advanced features to the framework, we recommend thoroughly reviewing both the
`SmartSim Documentation <https://www.craylabs.org/docs/overview.html>`_ and
`Stable-Baselines3 Documentation <https://stable-baselines3.readthedocs.io/en/master/>`_ documentation.

Below is a quick installation guide for all components. Note that Python 3.9–3.11 is required for compatibility.
We recommend starting with a fresh virtual environment:

.. code-block:: sh

   python -m venv /path/to/smartflow/environment
   source /path/to/smartflow/environment/bin/activate

SmartSim and SmartRedis
----------------------------

SmartSim provides the infrastructure for launching and managing simulations, while SmartRedis provides client
interfaces for interacting with the in-memory Redis database.

**Python Installation**

.. code-block:: sh

   # Install SmartSim
   pip install smartsim
   # Build SmartSim with CPU support and Dragon
   smart build --device cpu --dragon

   # Install SmartRedis Python client
   pip install smartredis

**C, C++, and Fortran Client Libraries**

For compiled language applications, the SmartRedis client libraries need to be built from source.
We currently use version 0.5.3 for compatibility.

.. note::
   The CFD solver, SmartRedis, and SmartRedis-MPI must be compiled with the same compiler to ensure proper linking.

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

Stable-Baselines3
----------------------------

Stable-Baselines3 is a Python library that provides implementations of reinforcement learning algorithms.
It can be installed with the following command:

.. code-block:: sh

   pip install stable-baselines3[extra]

SmartFlow
----------------------------

SmartFlow is installed with the following commands:

.. code-block:: sh

   cd SmartFlow
   pip install -e .

This will mark the current package as editable, so it can be modified and the changes will be automatically
available to the Python environment.

.. note::

   The scripts in SmartFlow rely on additional Python packages that may not be listed in the default installation.
   In particular, make sure the following libraries are installed:

   - ``omegaconf``
   - ``wandb``

   You can install them with:

   .. code-block:: sh

      pip install omegaconf wandb
      
SmartRedis-MPI
----------------------------

Before installing the CFD solver, build the SmartRedis-MPI library that will be linked by the MPI-based parallel CFD solver:

.. code-block:: sh

   git clone https://github.com/soaringxmc/smartredis-mpi.git
   cd smartredis-mpi

Edit the ``Makefile`` to set the correct paths to your SmartRedis installation:

.. code-block:: sh

   # Adjust the include and lib paths in the Makefile
   make

Add the library path to your environment:

.. code-block:: sh

   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/smartredis-mpi/lib
   # Add this to your .bashrc or .bash_profile for persistence

CFD Solver
----------------------------

The advantage of SmartFlow is that it can be easily integrated with any CFD solver. Only several lines of code
need to be added to the CFD solver to enable communication with the SmartFlow framework.

As an example, we only added five lines of code to the `CaLES <https://github.com/CaNS-World/CaLES>`_ solver
to enable its coupling with the SmartFlow framework.

If you want to use CaLES as your CFD solver or simply test the workflow of the SmartFlow framework,
please refer to the `CaLES <https://github.com/CaNS-World/CaLES>`_ for installation instructions.

.. note::

   - **Branch Selection**: Make sure to use the ``smartflow branch``, not the main branch.
   - **Build Configuration**: Ensure that the parameter ``PENCIL_AXIS`` is set to **3** in the ``build.conf`` file.



