## AWS DeepRacer

AWS DeepRacer is an **autonomous 1/18th-scale race car** and cloud simulation environment for learning **reinforcement learning (RL)** — a hands-on, gamified way to understand ML without prior ML experience.

**Key Concepts:**

- **Reinforcement Learning (RL)**: A type of ML where an agent learns by interacting with an environment — receives rewards for good actions and penalties for bad ones. The agent learns to maximize cumulative reward over time.
- **Agent**: The DeepRacer car (physical or simulated).
- **Environment**: The race track — simulated in AWS using a 3D physics engine.
- **Action Space**: The set of possible movements — speed and steering angle combinations.
- **Reward Function**: A Python function you write that returns a reward score based on the car's state (speed, steering angle, distance from center, progress on track). This is the main thing you tune.
- **Episode**: One complete lap attempt — from start until the car falls off the track or completes the lap.
- **Policy**: The model the agent learns — maps observed state → action.

**How it Works:**
1. Write a **reward function** in Python (the core of RL training).
2. Configure the training — action space, hyperparameters, training duration.
3. **Amazon SageMaker** trains the RL model in simulation using your reward function.
4. Evaluate in simulation — view video of the car navigating the track.
5. Deploy to the physical car or submit to **DeepRacer League** competitions.

**DeepRacer League:**
- AWS-organized racing competitions — virtual (simulated) and in-person (physical car on a track).
- Compete globally and at AWS re:Invent.
- Leaderboard rankings based on fastest lap time.

**Use cases:** Learning reinforcement learning concepts hands-on, team ML challenges and hackathons, introduction to ML for developers with no prior experience, AWS re:Invent competitions.
