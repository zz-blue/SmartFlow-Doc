SmartFlow
============================

**SmartFlow** is an open-source framework developed as a joint effort among researchers from various institutions to advance research in turbulence modeling, flow control, and numerical algorithm development through multi-agent deep reinforcement learning (DRL). SmartFlow leverages the `SmartSim <https://github.com/CrayLabs/SmartSim>`_ infrastructure library to efficiently launch and manage computational fluid dynamics (CFD) simulations. Data exchange between CFD simulations (implemented in Fortran or C++) and DRL models (implemented in Python) is seamlessly handled by `SmartRedis <https://github.com/CrayLabs/SmartRedis>`_  clients, ensuring efficient and scalable communication. The framework is optimized for deployment on high-performance computing (HPC) platforms, including both CPU clusters and GPU-accelerated architectures, enabling scalable and efficient training for computationally demanding problems. Built on top of `Relexi <https://github.com/flexi-framework/relexi>`_ and `SmartSOD2D <https://github.com/b-fg/SmartSOD2D>`_, SmartFlow offers the following two improvements:

1. **CFD-solver-agnostic framework**: To simplify the integration of diverse CFD solvers with the DRL framework, we have developed a data communication library `SmartRedis-MPI <https://github.com/soaringxmc/smartredis-mpi>`_ that can be easily linked to various CFD solvers. As an example, **only five lines of code** are needed to enable coupling between the `CaLES <https://github.com/CaNS-World/CaLES>`_ solver and the SmartFlow framework.

2. **PyTorch-based** `Stable-Baselines3 <https://stable-baselines3.readthedocs.io/en/master/>`_: Reinforcement learning algorithms are implemented using the widely adopted Stable-Baselines3 library, which is much easier to use, compared to the TensorFlow-based TF-Agents library used in previous implementations.

Please cite us if you find this framework useful!

.. seealso::  
   
   `Maochao Xiao, Francisco Alcántara-Ávila, Bernat Font, Marius Kurz, Di Zhou, Yuning Wang, Ting Zhu, Ricardo Vinuesa, Johan Larsson, and Sergio Pirozzoli, martFlow: An open-source framework for deep reinforcement learning in turbulence modeling, flow control and numerical algorithm development," presented at the 2nd European Fluid Dynamics Conference (EFDC2), Dublin, Ireland, 26–29 August 2025. <https://doi.org/10.1016/j.cpc.2025.109546>`_  


.. toctree::
   :maxdepth: 2
   :caption: Contents:

   How_it_works
   Installation
   Run_a_case
   Example
   Contributors
   Acknowledgements
