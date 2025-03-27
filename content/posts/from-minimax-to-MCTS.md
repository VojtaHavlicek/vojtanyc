+++
date = '2025-03-26'
title = "From Minimax to Monte Carlo Tree Search: Smarter AI for Mars Colony"
tags = ["AI", "game development", "reinforcement learning", "MCTS", "minimax", "typescript"]
description = "Documenting my journey from handcrafted heuristics to Monte Carlo Tree Search with learned evaluation"
+++

## The Problem

I've been working on the AI for my game, which takes place on a toroidal grid with dynamic systems like population growth, energy networks, and resource management. It’s a complex environment with a lot of long-term planning involved — and that makes writing a good AI surprisingly tricky.

Up until now, I’ve been using a classic **minimax** algorithm with a **handcrafted evaluation function**. Here's the setup:

- A minimax tree search (depth-limited)
- A fixed `evaluate()` function that tries to guess how "good" a state is, based on:
  - Population size
  - Energy yield
  - Proximity to resources
  - Habitat count
  - Loops in pipe networks (bad!)
  - And more...

While this approach works *okay* in simple cases, it really struggles in the kinds of situations my game cares most about — especially when **timing and sequence of actions** matter more than just static advantage.

## The Problem with Handcrafted Heuristics

The truth is, it’s *really hard* to come up with a good evaluation function that captures all the nuances of gameplay. Especially when:

- The value of actions changes over time
- There's hidden long-term payoff that heuristics can't "see"
- Loops or mistakes compound subtly

So, I started looking for better options — and landed on something much more exciting.

---

## The Plan: Monte Carlo Tree Search + Learned Evaluation

### Step 1: Basic MCTS with Random Rollouts

The first step is to implement **Monte Carlo Tree Search (MCTS)**, where instead of simulating all actions exhaustively like minimax, I simulate *random playthroughs* and average their outcomes.

The core ideas:
- Nodes represent game states
- Simulate random games to estimate how good a state is
- Use **UCT** (Upper Confidence Bound) to balance how deep vs. how wide does the algorithm explore game states.
- Backpropagate results to update parent nodes

### Step 2: Replace Random Rollouts with Heuristic Evaluation

As an intermediate step, I can plug in my existing `evaluate()` function instead of doing full random rollouts. This lets MCTS make faster decisions and deeper searches.

```ts
const value = evaluate(state); // heuristic instead of random rollout
```


### Step 3: Learn the Heuristic with Supervised Learning
Instead of trying to hand-tune the evaluation function, why not learn it?
Run MCTS on lots of game states
Save:
the state
the value (from MCTS)
the best action (optional)
Train a neural network to predict the value of a state
This gives me a value network I can use in future MCTS runs — just like AlphaZero.

### Step 4: Full Self-Play Reinforcement Learning
Once I’m comfortable with MCTS and training value networks, I want to move to self-play:
Let the AI play against itself
Use the results to continually improve the network
Train both value and policy (best move) predictions
Repeat!
Now I’ve entered the world of AlphaZero-style AI — tailored to my own game.

### Tech Stack Ideas

Although my game is built in TypeScript, I can log game state and train models using Python:
Training: PyTorch or TensorFlow
Storage: export game states as JSON
Replay and Simulation: headless version of game logic
MCTS: either in TypeScript or as a fast Python backend

### What's Next?

I'm currently implementing MCTS in TypeScript with my existing game logic. Once that's working, I'll:
Start logging training data
Train a simple model on MCTS values
Replace the heuristic with the model's output
Keep iterating!
This journey has already taught me a ton about AI, game design, and how even simple games can create deep strategy.
Stay tuned for more updates — and hopefully smarter AIs soon!
