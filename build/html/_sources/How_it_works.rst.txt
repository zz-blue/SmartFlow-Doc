How it works
============================

Most high-performance CFD solvers are written in low-level languages such as C/C++/Fortran, while ML libraries are typically available in Python or other high-level languages. This creates a "two-language problem" when data needs to be shared across different processes - a common challenge during online training of ML models where continuous data exchange occurs between the model and the CFD solver.

While this challenge can be addressed using Unix sockets or message-passing interface (MPI), SmartSim provides an elegant solution through a workflow library that facilitates communication between different instances via an in-memory Redis database. SmartRedis, which provides client libraries for C/C++/Fortran/Python, enables seamless communication with applications written in any of these languages.

The SmartRedis clients offer high-level API calls such as ``put_tensor`` and ``get_tensor`` that simplify sending and receiving data arrays, significantly reducing the overall software complexity. Additionally, SmartSim can manage processes, such as starting or stopping multiple CFD simulations, further streamlining workflow orchestration.
