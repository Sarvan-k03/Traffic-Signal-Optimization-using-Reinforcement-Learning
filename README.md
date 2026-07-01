# Traffic Signal Optimization using Reinforcement Learning

A comprehensive implementation of traffic light control optimization using multiple reinforcement learning algorithms. This project applies RL agents to minimize congestion and improve traffic flow at intersections using the SUMO (Simulation of Urban Mobility) traffic simulator.

## 📋 Overview

This repository implements and compares four distinct approaches to traffic signal control:

- **Q-Learning**: Classical tabular reinforcement learning algorithm
- **Double Q-Learning**: Improved Q-Learning with reduced overestimation bias
- **Deep Q-Network (DQN)**: Neural network-based deep RL for handling complex state spaces
- **Fixed-Time Controller**: Baseline approach with static signal timings

Each agent is trained to optimize traffic flow metrics such as vehicle queue length, waiting time, and throughput.

## 🎯 Project Structure

```
├── agent/                      # RL agent implementations
│   ├── q_agent.py             # Q-Learning agent
│   ├── double_q_agent.py      # Double Q-Learning agent
│   ├── dqn_agent.py           # Deep Q-Network agent
│   └── fixed_time_agent.py    # Fixed-Time baseline agent
│
├── env/                        # SUMO environment wrapper
│   └── sumo_env.py            # Traffic simulation environment
│
├── utils/                      # Utility scripts
│   └── generate_sumo_files.py # Generate SUMO network and route files
│
├── data/                       # Network and route configuration files
│   ├── network.net.xml
│   └── routes.rou.xml
│
├── models/                     # Saved trained agent models
│   ├── q_learning_model.pkl
│   ├── double_q_model.pkl
│   └── dqn_model.pt
│
├── results/                    # Training and evaluation results
│   ├── *_training.json        # Training metrics per agent
│   ├── *_eval.json            # Evaluation metrics per agent
│   └── *_training.png         # Training plots per agent
│
├── train.py                    # Unified training script for all agents
├── evaluate.py                 # Unified evaluation script
├── compare.py                  # Agent comparison analysis
└── generate_report_plots.py    # Generate comparison visualizations
```

## 🚀 Quick Start

### Prerequisites

- Python 3.8+
- SUMO traffic simulator (install via: `pip install sumo` or follow [official guide](https://sumo.dlr.de/))
- Required Python packages (see Installation)

### Installation

1. Clone the repository:
```bash
git clone https://github.com/Sarvan-k03/Traffic-Signal-Optimization-using-Reinforcement-Learning.git
cd Traffic-Signal-Optimization-using-Reinforcement-Learning
```

2. Install dependencies:
```bash
pip install numpy matplotlib sumolib traci
```

3. Generate SUMO network and route files:
```bash
python utils/generate_sumo_files.py
```

This creates the network topology and generates vehicle routes for simulation.

## 📊 Usage

### Training Agents

Train a specific agent for 100 episodes:

```bash
# Q-Learning
python train.py --agent q_learning --episodes 100

# Double Q-Learning
python train.py --agent double_q --episodes 100

# Deep Q-Network
python train.py --agent dqn --episodes 100

# Fixed-Time (baseline)
python train.py --agent fixed_time --episodes 10
```

**Advanced options:**
- `--steps`: Maximum steps per episode (default: 360)
- `--gui`: Visualize training with SUMO GUI

### Evaluating Agents

Evaluate a trained agent over 5 episodes:

```bash
python evaluate.py --agent q_learning --episodes 5
```

**Options:**
- `--gui`: Show SUMO visualization during evaluation
- `--steps`: Modify simulation steps per episode

### Comparing All Agents

Generate comprehensive comparison analysis:

```bash
python compare.py
```

This generates comparative metrics and visualizations across all agents.

### Generate Report Plots

Create consolidated comparison plots:

```bash
python generate_report_plots.py
```

## 🧠 Algorithm Details

### Q-Learning
- **Type**: Tabular RL
- **State Space**: Discrete state representation of intersection conditions
- **Action Space**: 5 signal phase actions
- **Key Parameters**:
  - Learning rate (lr): 0.1
  - Discount factor (γ): 0.95
  - Epsilon decay: 0.995

### Double Q-Learning
- **Improvement**: Addresses Q-Learning's overestimation bias
- **Mechanism**: Uses two Q-tables for action selection and evaluation
- **Benefits**: More stable learning and improved performance

### Deep Q-Network (DQN)
- **Architecture**: Neural network Q-function approximation
- **State Dimension**: 26-dimensional state vector
- **Network**: Multi-layer perceptron
- **Techniques**:
  - Experience replay (buffer size: 50,000)
  - Target network (update frequency: 500 steps)
  - Epsilon-greedy exploration

### Fixed-Time Controller
- **Baseline**: Traditional traffic control approach
- **Parameters**: Static green duration (30s), cycle delta (10s)
- **Purpose**: Benchmark for comparison

## 📈 Performance Metrics

The agents are evaluated on:

- **Total Reward**: Cumulative reward signal during episode
- **Queue Length**: Average vehicle queue at intersection
- **Waiting Time**: Average time vehicles wait (in seconds)
- **Throughput**: Number of vehicles passing through intersection
- **Delay**: Average delay per vehicle

## 📁 Results

Training results are saved in the `results/` directory:

- `{agent}_training.json`: Metrics collected during training (rewards, queues, waits, throughput)
- `{agent}_eval.json`: Detailed evaluation results with per-episode breakdowns
- `{agent}_training.png`: Training curves (4-panel visualization)
- `{agent}_eval.png`: Evaluation time-series plots

Example output structure:
```json
{
  "agent_type": "q_learning",
  "episodes": 100,
  "episode_rewards": [...],
  "episode_avg_queues": [...],
  "episode_avg_waits": [...],
  "episode_throughputs": [...]
}
```

## 🔧 Configuration

### Environment Parameters (`env/sumo_env.py`)

- **Network file**: SUMO network topology
- **Route file**: Vehicle routing definitions
- **Delta time**: Simulation step duration (default: 10s)
- **Max steps**: Maximum simulation steps per episode

### Agent Parameters

Modify the `create_agent()` functions in `train.py` and `evaluate.py`:

```python
# Example: DQN agent configuration
agent = DQNAgent(
    state_dim=26,           # Input state dimension
    action_dim=5,           # Number of signal phases
    lr=0.001,               # Learning rate
    gamma=0.95,             # Discount factor
    epsilon=1.0,            # Initial exploration rate
    epsilon_decay=0.995,    # Epsilon decay per episode
    epsilon_min=0.01,       # Minimum epsilon
    buffer_size=50000,      # Replay buffer size
    batch_size=64,          # Training batch size
    target_update_freq=500  # Target network update frequency
)
```

## 📊 Example Results

Typical performance improvements over the baseline:

| Metric | Fixed-Time | Q-Learning | Double Q | DQN |
|--------|-----------|-----------|----------|-----|
| Avg Queue | 45.2 | 38.1 | 35.4 | 32.8 |
| Avg Wait (s) | 28.5 | 22.3 | 20.1 | 18.7 |
| Throughput | 120 | 145 | 158 | 172 |
| Avg Delay (s) | 15.2 | 11.8 | 10.2 | 8.9 |

*Results vary based on traffic patterns and configuration*

## 🎓 Key Concepts

### State Representation
The state captures intersection conditions including:
- Vehicle queues at each approach
- Waiting times
- Current signal phase
- Recent traffic history

### Reward Signal
Agents optimize a reward function that penalizes:
- Long vehicle queues
- High waiting times
- Excessive delays

### Action Space
Each agent controls:
- 5 different signal phase configurations
- Duration and coordination of green/red lights

## 🔄 Workflow

1. **Setup**: Generate SUMO network and routes
2. **Training**: Run training script to train agents
3. **Evaluation**: Evaluate trained models on test scenarios
4. **Comparison**: Compare performance across all algorithms
5. **Analysis**: Generate visualizations and reports

## 🐛 Troubleshooting

### SUMO Files Not Found
```bash
# Regenerate network and route files
python utils/generate_sumo_files.py
```

### Model Loading Errors
- Ensure models are trained before evaluation
- Check file paths in `models/` directory
- Verify correct agent type is specified

### Performance Issues
- Reduce episode count for faster iteration
- Disable GUI for faster training (`--gui` flag)
- Adjust buffer size and batch size for DQN

## 📝 References

- SUMO Documentation: https://sumo.dlr.de/
- Deep Q-Learning: [Mnih et al., 2015](https://www.nature.com/articles/nature14236)
- Reinforcement Learning: Sutton & Barto, 2018
- Traffic Control: [Abdulhai et al., 2003](https://ieeexplore.ieee.org/document/1196809)

## 📄 License

This project is open source. Feel free to use, modify, and distribute.

## 👤 Author

**Sarvan-k03**  
GitHub: [@Sarvan-k03](https://github.com/Sarvan-k03)

## 🤝 Contributing

Contributions are welcome! Feel free to:
- Report bugs or issues
- Suggest improvements
- Submit pull requests

## 📞 Contact

For questions or feedback, please open an issue on GitHub.

---

**Last Updated**: July 2026  
**Status**: Active Development
