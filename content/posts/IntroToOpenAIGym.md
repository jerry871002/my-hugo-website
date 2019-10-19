---
title: "Intro to Open AI Gym"
subtitle: "Brief Introduction"
date: 2019-10-19T22:34:53+08:00
tags: ["Python"]
draft: false
toc: false
---

## Import packages we need


```python
import gym
import random
import numpy as np
```

## Environment with Discrete Actions

Check the observation space and action space by using `env.observation_space` and `env.action_space`


```python
env_name = 'CartPole-v1'
env = gym.make(env_name)
print("Observation space:", env.observation_space)
print("Action space:", env.action_space)
```

    Observation space: Box(4,)
    Action space: Discrete(2)



```python
class Agent:
    def __init__(self, env):
        self.action_size = env.action_space.n
        print("Action size:", self.action_size)

    def get_action(self, state):
        #action = random.choice(range(self.action_size)) # choose an action randomly
        pole_angle = state[2]
        action = 0 if pole_angle < 0 else 1
        return action
```


```python
agent = Agent(env)

for _ in range(5):
    state = env.reset()
    total_reward = 0

    for _ in range(200):
        #action = env.action_space.sample() # choose an action randomly
        action = agent.get_action(state)
        state, reward, done, info = env.step(action)
        total_reward += reward
        env.render()

        if done:
            print(total_reward)
            break

env.close()
```

    Action size: 2
    38.0
    46.0
    55.0
    51.0
    47.0


## Environment with Continuous Actions


```python
env_name = 'MountainCarContinuous-v0'
env = gym.make(env_name)
print("Observation space:", env.observation_space)
print("Action space:", env.action_space)
```

    Observation space: Box(2,)
    Action space: Box(1,)



```python
print(env.action_space.high)
print(env.action_space.low)
print(env.action_space.shape)
```

    [1.]
    [-1.]
    (1,)



```python
print(env.action_space.n)
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-29-335df5060464> in <module>
    ----> 1 print(env.action_space.n)


    AttributeError: 'Box' object has no attribute 'n'


> Since the actions are continuous, there is no attribute 'n'


```python
class Agent:
    def __init__(self, env):
        self.action_low = env.action_space.low
        self.action_high = env.action_space.high
        self.action_shape = env.action_space.shape
        print("Action range:", self.action_low, self.action_high)

    def get_action(self, state):
        # choose an action randomly
        action = np.random.uniform(low=self.action_low,
                                   high=self.action_high,
                                   size=self.action_shape)
        return action
```


```python
agent = Agent(env)
state = env.reset()

for _ in range(200):
    action = agent.get_action(state)
    state, reward, done, info = env.step(action)
    env.render()

    if done:
        break

env.close()
```

    Action range: [-1.] [1.]


## Agent that handles both discrete and continuous actions

Check the type of the `action_space` by calling `type()`


```python
env_name = 'CartPole-v1'
env = gym.make(env_name)
print(env_name, type(env.action_space))

env_name = 'MountainCarContinuous-v0'
env = gym.make(env_name)
print(env_name, type(env.action_space))
```

    CartPole-v1 <class 'gym.spaces.discrete.Discrete'>
    MountainCarContinuous-v0 <class 'gym.spaces.box.Box'>



```python
class Agent:
    def __init__(self, env):
        self.is_discrete = \
            type(env.action_space) == gym.spaces.discrete.Discrete

        if self.is_discrete:
            self.action_size = env.action_space.n
            print("Action size:", self.action_size)
        else:
            self.action_low = env.action_space.low
            self.action_high = env.action_space.high
            self.action_shape = env.action_space.shape
            print("Action range:", self.action_low, self.action_high)

    def get_action(self, state):
        if self.is_discrete:
            action = random.choice(range(self.action_size))
        else:
            action = np.random.uniform(low=self.action_low,
                                   high=self.action_high,
                                   size=self.action_shape)
        return action
```

* Try discrete first(`Acrobot-v1`)


```python
env_name = 'Acrobot-v1'
env = gym.make(env_name)

agent = Agent(env)
state = env.reset()

for _ in range(200):
    action = agent.get_action(state)
    state, reward, done, info = env.step(action)
    env.render()

    if done:
        break

env.close()
```

        Action size: 3


* Then the continuous one(`Pendulum-v0`)


```python
env_name = 'Pendulum-v0'
env = gym.make(env_name)

agent = Agent(env)
state = env.reset()

for _ in range(200):
    action = agent.get_action(state)
    state, reward, done, info = env.step(action)
    env.render()

    if done:
        break

env.close()
```

        Action range: [-2.] [2.]
