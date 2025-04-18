Example
====================================
This example demonstrates how to train a reinforcement learning agent using the SmartFlow framework on a turbulent channel flow case at a friction Reynolds number of Ret = 5200.

Flow Case Configuration
----------------------------

The CFD solver used in this case is `CaLES <https://github.com/CaNS-World/CaLES>`_, configured specifically for channel flow at Ret = 5200.  
For details on the CaLES case setup, please refer to the `flow_cases <https://github.com/soaringxmc/SmartFlow/tree/main/examples/flow_cases>`_ directory.

Training Configuration
---------------------------

The following YAML-style settings define the training environment and parameters:

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
      n_cfds: 2
      agents_per_cfd: 48
      tasks_per_cfd: 4
      cfd_state_dim: 2
      cfd_action_dim: 1
      cfd_reward_dim: 2
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
      executable_path: "/scratch/maochao/code/CaLES/build/cales"
      case_folder: "../flow_cases"

    runner:
      mode: "train"
      restart: false
      policy: "MlpPolicy"
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

The following settings are used for evaluating a trained policy.


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
        runner.model_load_path="/scratch/maochao/code/SmartFlow/experiments/train_retau_05200/models/final/yjkxqlf3" \
        runner.steps_per_episode=3600 \
        > out 2> err