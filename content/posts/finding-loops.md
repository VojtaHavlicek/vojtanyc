+++
date = '2025-03-23T12:04:56-04:00'
draft = true
title = 'Finding Loops on Torus'
tags = ["AI", "game development", "typescript"]
+++

## 🧩 Problem Statement

In my current game project, I'm working with a toroidal 2D grid (a grid that wraps around like a donut). On this grid, certain tiles are marked as “paths.” These paths can form closed loops, and I wanted a way to **detect and count the number of such loops**.

There’s a twist:  
- The grid **wraps around both horizontally and vertically** (like Pac-Man).  
- I only get the path data as a **list of coordinates**, not a full 2D array.  
- And there may be **multiple disconnected path components** on the grid.

## 🧠 Strategy

Instead of trying to trace every possible loop (which is hard and inefficient), I used a classic trick from graph theory:

> For any undirected graph, the number of fundamental cycles (independent loops) is given by:  
>  
> `loops = edges - vertices + components`

This formula lets us **count** loops without having to explicitly find them.

### Step-by-step:

1. Treat each path point as a node.
2. Connect each node to its 4 toroidal neighbors (up, down, left, right) if they also exist in the point list.
3. For each **connected component**:
   - Count nodes `V`
   - Count edges `E` (carefully, avoiding duplicates)
   - Compute `loops = E - V + 1`
4. Sum over all components.

## 💻 TypeScript Implementation

Here's the final version of the loop-counting function I integrated into my game:

```ts
type Point = [number, number];

function countLoops(points: Point[], n: number): number {
  const pointSet = new Set(points.map(([y, x]) => `${y},${x}`));
  const visited = new Set<string>();
  let totalLoops = 0;

  function getNeighbors(y: number, x: number): Point[] {
    return [
      [(y + 1) % n, x],
      [y, (x + 1) % n],
      [(y - 1 + n) % n, x],
      [y, (x - 1 + n) % n]
    ];
  }

  function bfsComponent(start: Point): number {
    const queue: Point[] = [start];
    const componentNodes = new Set<string>();
    const componentEdges = new Set<string>();

    while (queue.length > 0) {
      const [y, x] = queue.shift()!;
      const key = `${y},${x}`;
      if (visited.has(key)) continue;

      visited.add(key);
      componentNodes.add(key);

      for (const [ny, nx] of getNeighbors(y, x)) {
        const neighborKey = `${ny},${nx}`;
        if (pointSet.has(neighborKey)) {
          const edgeKey = [key, neighborKey].sort().join("-");
          componentEdges.add(edgeKey);

          if (!visited.has(neighborKey)) {
            queue.push([ny, nx]);
          }
        }
      }
    }

    const V = componentNodes.size;
    const E = componentEdges.size;
    return E - V + 1;
  }

  for (const [y, x] of points) {
    const key = `${y},${x}`;
    if (!visited.has(key)) {
      totalLoops += bfsComponent([y, x]);
    }
  }

  return totalLoops;
}
```

### 🧪 Next Steps

I plan to:
* Highlight loop regions visually in-game
* Use this for puzzle validation
* Maybe count loop area or bounding boxes
Hope this breakdown helps someone else working on grid-based mechanics or cellular automata!