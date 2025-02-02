+++
date = '2025-02-01T19:10:42-05:00'
draft = false
title = 'Writing a turn-based strategy game bot in LLMs'
+++

## Why am I writing a bot for a turn-based game in 2025? 
We recently developed a turn based strategy [game](https://martians-nine.vercel.app) with Michal Srb. The multiplayer is done, but what remains is the AI. This series of blogposts will follow my journey towards implementing a (hopefully) decent oponnent. 

From what I know now, it seems to be the best to:
1) Implement a basic minimax strategy on a simpler game.
2) Implement interesting heuristics on that simpler game. 
3) Use Monte-Carlo tree search on that simpler game.
4) Port one of those strategies to our game.
5) Keep an eye our on the LLMs in the process. I will implement both a strategist that uses LLM queries and use LLMs to help me with the code. 

### Minimax
The simplest game I could think of is Tic Tac Toe and it's bigger brother called [Piskvorky](https://cs.wikipedia.org/wiki/Piškvorky) (Czech only) or [Gomoku](https://en.wikipedia.org/wiki/Gomoku). A simple warmup is a Tic Tac Toe on 3x3 board. 


