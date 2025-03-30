+++
title: "From Minimax to Monte Carlo Tree Search: Progress Update"
date: 2025-03-28
tags: ["AI", "gamedev", "typescript", "MCTS", "devlog"]
description: "A behind-the-scenes look at switching my game AI from heuristics to Monte Carlo Tree Search"
+++

## AI Evolution: From Minimax to MCTS

Over the past few days, I've made some big strides in improving the AI for my game. What started as a basic **minimax + heuristic** approach quickly ran into limitations. The game is timing-sensitive and rewards long-term planning — not something minimax handles easily with shallow depth and static evaluations.

So, I switched gears and started working on a **Monte Carlo Tree Search (MCTS)** implementation — and I’m really happy with the progress.

---

## Why MCTS?

Unlike minimax, MCTS doesn't require a perfect evaluation function. Instead, it simulates random games from each possible move, gradually building up a search tree that favors actions with better long-term outcomes.

This suits my game well because:
- The state space is large
- There’s a lot of deferred payoff (e.g., energy, population)
- Strategic mistakes often emerge over several turns

---

## Implementing Basic MCTS

Here's what the MCTS algorithm does:

1. **Selection**: Traverse the tree using UCT to find the most promising node
2. **Expansion**: Add a new child node for an unexplored move
3. **Simulation**: Play out a random game (or use a simple heuristic) from that node
4. **Backpropagation**: Update node values with the result

Initially, I ran with ~1000 simulations per move. Even with just random playouts, the results were **noticeably better** than my hand-crafted minimax player.

---

## Fixing a Sneaky Bug: Turn Alternation

While testing MCTS, I noticed something odd — the AI was sometimes playing multiple turns in a row. After digging in, I realized my `move()` function didn’t automatically check if the current player had used up their action points.

This meant turns weren’t advancing unless I explicitly called `nextTurn()` (which I only did in the case of a `doNothing` move).

### The fix:
I updated `move()` to automatically call `nextTurn()` once the player has taken the full number of allowed actions. This not only fixed the turn order, but also simplified the AI loop in `nextTurn()` — no more `while` loop necessary!

---

## Logging for Future Learning

Now that the MCTS core is working, I’m starting to **log training data** from each move the AI makes:
- The full game state
- The action probabilities from the MCTS visit counts (a proxy for policy)
- The final game result (used as a value target)

This will allow me to train a neural network later — either to **replace the heuristic** or even to guide MCTS itself.

Next steps:
- Save training data to disk (or send to backend)
- Design a small policy + value network in PyTorch
- Try supervised training on logged MCTS outputs

---

## What’s Next?

I’m planning to:
- Improve rollout quality during MCTS (currently purely random)
- Add light heuristics for resource proximity
- Begin training a model from logged games
- Eventually move toward AlphaZero-style **self-play reinforcement learning**

---

## Final Thoughts

This has been one of the most fun and educational parts of the project so far. MCTS feels like a really natural fit for the game’s pacing and complexity. It’s also opened the door to future learning-based upgrades without committing to full-blown reinforcement learning from the start.

Stay tuned for the next chapter — neural nets, training loops, and maybe even a visual MCTS tree viewer! 

