# Snake AI Optimization: Food & Survival Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Refine the Snake AI and food system to ensure unique naming, tighter food timing (3 apples, delayed refresh), and a smarter survival algorithm using Flood Fill to avoid dead ends.

**Architecture:** 
- Enhance `resetGame` with a `usedNames` set for unique identification.
- Refactor `spawnFood` to use a 1-3s delayed queuing system for maintaining 3 apples.
- Update `FOOD_TYPES` durations (5s/3.5s/2s).
- Implement `countReachableArea` (Flood Fill) and integrate it into a new `getSafeMove` decision layer.

**Tech Stack:** JavaScript (Canvas-based game), A* Pathfinding, Flood Fill Algorithm.

---

### Task 1: Unique Naming & Duration Update

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Define `usedNames` and update `FOOD_TYPES` durations**

```javascript
// Around line 40-50
const FOOD_TYPES = {
    NORMAL: { color: '#ff0044', score: 1, duration: 5000, chance: 0.7 },
    RARE: { color: '#ffcc00', score: 3, duration: 3500, chance: 0.2 },
    LEGENDARY: { color: '#aa00ff', score: 10, duration: 2000, chance: 0.1 }
};

let usedNames = new Set();
```

- [ ] **Step 2: Update `resetGame` for unique name picking**

```javascript
function resetGame() {
    updateLeaderboard();
    
    let availableNames = namePool.filter(n => !usedNames.has(n));
    if (availableNames.length === 0) {
        usedNames.clear();
        availableNames = namePool;
    }
    currentRoundSnakeName = availableNames[Math.floor(Math.random() * availableNames.length)];
    usedNames.add(currentRoundSnakeName);

    snakes = [initSnake(currentRoundSnakeName)];
    score = 0;
    foods = [];
    spawnFood();
    document.getElementById('score').innerText = `Score: ${score}`;
    gameRunning = true;
}
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "refactor: implement unique naming and shorten food durations"
```

---

### Task 2: 3-Apple Limit & Delayed Refresh

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Implement delayed food spawning**

```javascript
// Add at top level
let pendingSpawnCount = 0;

function spawnFood() {
    const maxFoods = 3;
    const currentActive = foods.length;
    const totalExistingOrPending = currentActive + pendingSpawnCount;

    if (totalExistingOrPending < maxFoods) {
        const needed = maxFoods - totalExistingOrPending;
        for (let i = 0; i < needed; i++) {
            pendingSpawnCount++;
            const delay = 1000 + Math.random() * 2000; // 1-3s
            setTimeout(() => {
                spawnOneFood();
                pendingSpawnCount--;
            }, delay);
        }
    }
}

function spawnOneFood() {
    const typeRoll = Math.random();
    let type = (typeRoll < 0.1) ? 'LEGENDARY' : (typeRoll < 0.3) ? 'RARE' : 'NORMAL';

    const newFood = {
        x: Math.floor(Math.random() * tileCount),
        y: Math.floor(Math.random() * tileCount),
        type: type,
        expires: Date.now() + FOOD_TYPES[type].duration
    };

    if (!isOccupied(newFood.x, newFood.y) && !foods.some(f => f.x === newFood.x && f.y === newFood.y)) {
        foods.push(newFood);
    } else {
        // Retry immediately if spot occupied
        spawnOneFood();
    }
}
```

- [ ] **Step 2: Update existing calls to `spawnFood()`**
Ensure `moveSnake` and `gameLoop` use the new `spawnFood()`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: limit to 3 apples with 1-3s spawn delay"
```

---

### Task 3: Flood Fill AI & Dead-end Prevention

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Implement `countReachableArea`**

```javascript
function countReachableArea(startX, startY, snake) {
    const queue = [{x: startX, y: startY}];
    const visited = new Set();
    const key = (p) => `${p.x},${p.y}`;
    visited.add(key({x: startX, y: startY}));
    let count = 0;

    while (queue.length > 0) {
        const curr = queue.shift();
        count++;

        const neighbors = [
            {x: curr.x+1, y: curr.y}, {x: curr.x-1, y: curr.y},
            {x: curr.x, y: curr.y+1}, {x: curr.x, y: curr.y-1}
        ].filter(p => p.x >= 0 && p.x < tileCount && p.y >= 0 && p.y < tileCount)
         .filter(p => !visited.has(key(p)) && !isOccupied(p.x, p.y));

        for (const n of neighbors) {
            visited.add(key(n));
            queue.push(n);
        }
    }
    return count;
}
```

- [ ] **Step 2: Upgrade `findSurvivalMove` and `gameLoop` decision logic**

```javascript
function findSurvivalMove(snake) {
    const head = snake.body[0];
    const moves = [
        {x: head.x+1, y: head.y}, {x: head.x-1, y: head.y},
        {x: head.x, y: head.y+1}, {x: head.x, y: head.y-1}
    ].filter(p => p.x >= 0 && p.x < tileCount && p.y >= 0 && p.y < tileCount)
     .filter(p => !isOccupied(p.x, p.y));

    if (moves.length === 0) return;

    let bestMove = moves[0];
    let maxArea = -1;

    for (const m of moves) {
        const area = countReachableArea(m.x, m.y, snake);
        if (area > maxArea) {
            maxArea = area;
            bestMove = m;
        }
    }
    snake.dx = bestMove.x - head.x;
    snake.dy = bestMove.y - head.y;
}
```

- [ ] **Step 3: Integrate Flood Fill check into `gameLoop`**
Before committing to an A* path, check if the first step of that path leaves enough room (e.g., area > snake.body.length).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: implement Flood Fill for better AI survival"
```

---

### Task 4: Final Verification

**Files:**
- Manual test in browser.

- [ ] **Step 1: Verify Names** (Restart game 20 times, ensure no repeats until pool resets)
- [ ] **Step 2: Verify Food Counts** (Max 3, watch the 1-3s delay)
- [ ] **Step 3: Verify Durations** (Watch Legendary apple disappear in 2s)
- [ ] **Step 4: Verify Survival** (Watch snake avoid self-trapping in large open spaces)
