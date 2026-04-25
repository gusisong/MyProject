# 自驱动贪吃蛇 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 实现一个单文件的 Web 版 AI 自驱动贪吃蛇，包含 A* 寻路和血量系统。

**Architecture:** 采用单文件 HTML5 Canvas 架构，核心逻辑分为游戏引擎、A* 寻路 AI 和渲染层。

**Tech Stack:** JavaScript (ES6+), HTML5 Canvas, CSS3.

---

### Task 1: 基础 HTML 结构与 Canvas 初始化

**Files:**
- Create: `/home/gusisong/Code/MyPoject/index.html`

- [ ] **Step 1: 编写基础 HTML 和 CSS**

```html
<!DOCTYPE html>
<html>
<head>
    <title>AI Snake Prototype</title>
    <style>
        body { background: #1a1c2c; display: flex; flex-direction: column; align-items: center; color: #fff; font-family: monospace; }
        #ui { width: 400px; display: flex; justify-content: space-between; padding: 10px; }
        #health-bar { display: flex; gap: 2px; }
        .hp-segment { width: 10px; height: 10px; background: #ff0044; border: 1px solid #000; }
        .hp-segment.lost { background: #333; }
        canvas { border: 4px solid #333; image-rendering: pixelated; }
    </style>
</head>
<body>
    <div id="ui">
        <div id="health-bar"></div>
        <div id="score">Score: 0</div>
    </div>
    <canvas id="gameCanvas" width="400" height="400"></canvas>
    <script>
        // Placeholder for game logic
    </script>
</body>
</html>
```

- [ ] **Step 2: 初始化 Canvas 渲染上下文**

```javascript
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const gridSize = 20;
const tileCount = 20;

function drawBackground() {
    ctx.fillStyle = '#1a1c2c';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    ctx.strokeStyle = '#222';
    for(let i=0; i<tileCount; i++) {
        for(let j=0; j<tileCount; j++) {
            ctx.strokeRect(i*gridSize, j*gridSize, gridSize, gridSize);
        }
    }
}
drawBackground();
```

- [ ] **Step 3: 提交代码**

```bash
git add index.html
git commit -m "feat: initialize canvas and basic UI"
```

---

### Task 2: 核心游戏逻辑与实体定义

**Files:**
- Modify: `/home/gusisong/Code/MyPoject/index.html`

- [ ] **Step 1: 定义 Snake 和 Food 类**

```javascript
let snake = [{x: 10, y: 10}];
let food = {x: 5, y: 5};
let health = 20;
let score = 0;
let dx = 0;
let dy = 0;

function updateHealthUI() {
    const bar = document.getElementById('health-bar');
    bar.innerHTML = '';
    for(let i=0; i<20; i++) {
        const seg = document.createElement('div');
        seg.className = 'hp-segment' + (i >= health ? ' lost' : '');
        bar.appendChild(seg);
    }
}
```

- [ ] **Step 2: 实现移动与碰撞检测**

```javascript
function moveSnake() {
    const head = {x: snake[0].x + dx, y: snake[0].y + dy};
    
    // Wall collision
    if (head.x < 0 || head.x >= tileCount || head.y < 0 || head.y >= tileCount) {
        health--;
        updateHealthUI();
        return false; // Stop movement on wall hit
    }

    // Self collision
    if (snake.some(s => s.x === head.x && s.y === head.y)) {
        alert('Game Over: Bit yourself!');
        resetGame();
        return false;
    }

    snake.unshift(head);
    if (head.x === food.x && head.y === food.y) {
        score++;
        document.getElementById('score').innerText = `Score: ${score}`;
        spawnFood();
    } else {
        snake.pop();
    }
    return true;
}
```

- [ ] **Step 3: 提交代码**

```bash
git add index.html
git commit -m "feat: add core game mechanics and health system"
```

---

### Task 3: 实现 A* 寻路 AI

**Files:**
- Modify: `/home/gusisong/Code/MyPoject/index.html`

- [ ] **Step 1: 编写 A* 核心函数**

```javascript
function getPath(start, target) {
    const openSet = [start];
    const cameFrom = new Map();
    const gScore = {};
    const fScore = {};
    const key = p => `${p.x},${p.y}`;

    gScore[key(start)] = 0;
    fScore[key(start)] = Math.abs(start.x - target.x) + Math.abs(start.y - target.y);

    while (openSet.length > 0) {
        openSet.sort((a, b) => fScore[key(a)] - fScore[key(b)]);
        const current = openSet.shift();

        if (current.x === target.x && current.y === target.y) {
            const path = [];
            let curr = current;
            while (cameFrom.has(key(curr))) {
                path.push(curr);
                curr = cameFrom.get(key(curr));
            }
            return path.reverse();
        }

        const neighbors = [
            {x: current.x+1, y: current.y}, {x: current.x-1, y: current.y},
            {x: current.x, y: current.y+1}, {x: current.x, y: current.y-1}
        ].filter(p => p.x >= 0 && p.x < tileCount && p.y >= 0 && p.y < tileCount)
         .filter(p => !snake.some(s => s.x === p.x && s.y === p.y));

        for (let neighbor of neighbors) {
            let tentativeGScore = gScore[key(current)] + 1;
            if (tentativeGScore < (gScore[key(neighbor)] || Infinity)) {
                cameFrom.set(key(neighbor), current);
                gScore[key(neighbor)] = tentativeGScore;
                fScore[key(neighbor)] = tentativeGScore + Math.abs(neighbor.x - target.x) + Math.abs(neighbor.y - target.y);
                if (!openSet.some(p => p.x === neighbor.x && p.y === neighbor.y)) {
                    openSet.push(neighbor);
                }
            }
        }
    }
    return null;
}
```

- [ ] **Step 2: 集成 AI 到游戏循环**

```javascript
function gameLoop() {
    const path = getPath(snake[0], food);
    if (path && path.length > 0) {
        dx = path[0].x - snake[0].x;
        dy = path[0].y - snake[0].y;
    } else {
        // Survival mode: move to longest available space
        findSurvivalMove();
    }
    moveSnake();
    draw();
    if (health > 0) setTimeout(gameLoop, 100);
}
```

- [ ] **Step 3: 提交代码**

```bash
git add index.html
git commit -m "feat: implement A* pathfinding AI"
```

---

### Task 4: 细节抛光与像素风渲染

**Files:**
- Modify: `/home/gusisong/Code/MyPoject/index.html`

- [ ] **Step 1: 完善绘制函数 (像素风)**

```javascript
function draw() {
    drawBackground();
    // Draw Food
    ctx.fillStyle = '#ff0044';
    ctx.fillRect(food.x*gridSize+2, food.y*gridSize+2, gridSize-4, gridSize-4);
    ctx.fillStyle = '#fff';
    ctx.fillRect(food.x*gridSize+4, food.y*gridSize+4, 4, 4);

    // Draw Snake
    snake.forEach((s, i) => {
        ctx.fillStyle = i === 0 ? '#fff' : '#33e033';
        ctx.fillRect(s.x*gridSize+1, s.y*gridSize+1, gridSize-2, gridSize-2);
    });
}
```

- [ ] **Step 2: 最终测试与提交**

```bash
git add index.html
git commit -m "style: polish pixel art rendering"
```
