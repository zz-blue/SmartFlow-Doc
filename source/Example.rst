Example
====================================
After the Smartflow framework installation, you can couple the Smartflow framework with different CFD solvers for reinforcement learning (RL) applications. This example demonstrates how to train a reinforcement learning agent using the SmartFlow framework on a turbulent channel flow case at a friction Reynolds number of Ret = 5200. The case is located in the ``/your/smartflow/path/SmartFlow/examples/train_retau_05200`` directory. The training process is based on the MlpPolicy, and the trained agent is evaluated on the same case.

Flow Case Configuration
----------------------------

The CFD solver used in this case is `CaLES <https://github.com/CaNS-World/CaLES>`_, configured specifically for channel flow at Ret = 5200.  
For details on the CaLES case setup, please refer to the `flow_cases <https://github.com/soaringxmc/SmartFlow/tree/main/examples/flow_cases>`_ directory.

Training Configuration
---------------------------

The following YAML-style settings define the training environment and parameters as an example, and you may need to adjust them according to your specific requirements. In general, each case has its own configuration file, i.e., `config.yaml`, which contains the settings for the environment, runner, and other parameters.Taking the `retau_05200` case as an example, the configuration file is located in the ``/your/samrtflow/path/SmartFlow/examples/train_retau_05200`` directory. The configuration file is as follows:

config.yaml
^^^^^^^^^^^^^^^^^^

.. code-block:: yaml

    wandb:
      project: "channel"
      run_name: "train-retau-05200"
      mode: "online"
      sync_tensorboard: true
      save_code: true

    environment:
      # The following settings are used for the environment. You may need to adjust them according to your specific requirements.
      # The number of CFD
      n_cfds: 2
      # The number of agents per CFD
      agents_per_cfd: 48
      # The number of tasks (processors) per CFD, which is the number of MPI ranks
      # Please note it should not exceed the number of cores your server has
      tasks_per_cfd: 4
      # Your desired state, action, and reward dimensions per your project requirements
      cfd_state_dim: 2
      cfd_action_dim: 1
      cfd_reward_dim: 2
      # The number of agent state, action, and reward dimensions per your project requirements
      agent_state_dim: 2
      agent_action_dim: 1
      # The number of CFD steps per agent's each action
      cfd_steps_per_action: 10
      agent_interval: 4
      neighbor_count: 0
      poll_time: 360000000
      verbosity: "debug"
      save_trajectories: true
      trajectory_path: "trajectories"
      cfd_dtype: "float64"
      action_bounds: [-1.0, 1.0]
      # The reward beta value, which is used to balance the global reward and local rewards in this step
      reward_beta: 0.2
      # The case names for the CFD case
      case_names: ["retau_05200"]
      # You may need to adjust the CaLES executable path and case folder according to your setup below:
      executable_path: "/scratch/maochao/code/CaLES/build/cales"  # Path to the CaLES executable
      case_folder: "../flow_cases"

    runner:
      mode: "train"
      restart: false
      policy: "MlpPolicy" # The policy used for training agents
      reset_num_timesteps: true
      total_cfd_episodes: 3200
      steps_per_episode: 120
      hidden_layers: [128, 128]
      learning_rate: 5e-4
      log_interval: 1
      summary_interval: 1
      seed: 16
      ckpt_num: 1000000
      ckpt_interval: 1
      batch_size: 1

    logging:
      save_dir: '../runs'

    smartsim:
      n_dbs: 1
      network_interface: "lo"
      run_command: "mpirun"
      launcher: "local"

    extras:
      n_cells: 16
      tauw_min_percent: 0.8
      tauw_max_percent: 1.2
      hwm_min: 0.075
      hwm_max: 0.150
      kap_log: 0.41

train.sh
^^^^^^^^^^^^

Before starting the training, remove any previous logs and caches:

.. code-block:: bash

    rm -r __pycache__ trajectories envs tensorboard_logs wandb models/checkpoints

Run the training script with the following command:

.. code-block:: bash

    python -u ../../src/smartflow/main.py \
        runner.mode=train \
        runner.total_cfd_episodes=400 \
        runner.restart=False \
        runner.reset_num_timesteps=True \
        > out 2> err


Evaluation Configuration
---------------------------

The following settings are used for evaluating a trained policy. Similar to the training configuration, you may need to adjust them according to your specific requirements. Taking the case above as the example, the evaluation configuration file is located in the ``/your/smartflow/path/SmartFlow/examples/eval_retau_05200`` directory. The configuration file is as follows:


config.yaml
^^^^^^^^^^^^^^^^^^

.. code-block:: yaml

    wandb:
    project: "channel"
    run_name: "eval-retau-05200"
    mode: "online"
    sync_tensorboard: true
    # group: null  # Optional
    # tags: []     # Optional
    save_code: true

    environment:
    n_cfds: 1
    agents_per_cfd: 48
    tasks_per_cfd: 4
    cfd_state_dim: 2
    cfd_reward_dim: 2
    cfd_action_dim: 1
    agent_state_dim: 2
    agent_action_dim: 1
    cfd_steps_per_action: 10
    agent_interval: 4
    neighbor_count: 0
    poll_time: 360000000
    verbosity: "debug"
    save_trajectories: true
    trajectory_path: "trajectories"
    cfd_dtype: "float64"
    action_bounds: [-1.0, 1.0]
    reward_beta: 0.2
    case_names: ["retau_05200"]
    # You may need to adjust the CaLES executable path according to your specific dictionary. 
    executable_path: "/scratch/maochao/code/CaLES/build/cales"
    case_folder: "../flow_cases"

    runner:
    mode: "eval"
    restart: false
    policy: "MlpPolicy"
    reset_num_timesteps: true
    total_cfd_episodes: 1
    steps_per_episode: 120
    hidden_layers: [128, 128]
    learning_rate: 5e-4
    log_interval: 1
    summary_interval: 1
    seed: 16
    ckpt_num: 1000000
    ckpt_interval: 1
    batch_size: 1

    logging:
    save_dir: '../runs'

    smartsim:
    n_dbs: 1
    network_interface: "lo"
    run_command: "mpirun"
    launcher: "local"

    extras:
    n_cells: 16
    tauw_min_percent: 0.8
    tauw_max_percent: 1.2
    hwm_min: 0.075
    hwm_max: 0.150
    kap_log: 0.41

eval.sh
^^^^^^^^^^^^

Before starting the evaluation, remove any previous logs and caches:

.. code-block:: bash

    rm -r __pycache__ trajectories envs tensorboard_logs wandb

Run the evaluation script with the following command:

.. code-block:: bash

    python -u ../../src/smartflow/main.py \
        # You may need to adjust the path to the trained model according to your setup.
        runner.model_load_path="/scratch/maochao/code/SmartFlow/experiments/train_retau_05200/models/final/yjkxqlf3" \
        runner.steps_per_episode=3600 \
        > out 2> err