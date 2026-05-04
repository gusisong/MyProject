# Snake AI Optimization: Balanced Food Timing & Tiered Spawn

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Further compress food durations and implement a tiered spawn distance rule to ensure apples spawn away from the head while remaining reachable.

**Architecture:**
- Update `FOOD_TYPES` durations (3.5s/2.5s/1.4s).
- Refactor `spawnOneFood` to accept a `minDist` parameter.
- Implement tiered radius selection based on food type.
- Implement a tiered fallback strategy for spawn distance.

**Tech Stack:** JavaScript (Canvas-based game), Manhattan Distance.

---

### Task 1: Compressed Durations & Tiered Config

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Update `FOOD_TYPES` with compressed durations**

```javascript
const FOOD_TYPES = {
    NORMAL: { color: '#ff0044', score: 1, duration: 3500, chance: 0.7, minRadius: 15 },
    RARE: { color: '#ffcc00', score: 3, duration: 2500, chance: 0.2, minRadius: 10 },
    LEGENDARY: { color: '#aa00ff', score: 10, duration: 1400, chance: 0.1, minRadius: 5 }
};
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "refactor: compress food durations and add minRadius config"
```

---

### Task 2: Tiered Distance Spawning

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Refactor `spawnOneFood` to implement tiered distance and fallback**

```javascript
function spawnOneFood(retryCount = 0) {
    if (retryCount > 20) return; // Increased retry limit for distance checks
    
    const typeRoll = Math.random();
    let type;
    if (typeRoll < FOOD_TYPES.LEGENDARY.chance) {
        type = 'LEGENDARY';
    } else if (typeRoll < FOOD_TYPES.LEGENDARY.chance + FOOD_TYPES.RARE.chance) {
        type = 'RARE';
    } else {
        type = 'NORMAL';
    }

    const nx = Math.floor(Math.random() * tileCount);
    const ny = Math.floor(Math.random() * tileCount);
    
    // Check occupancy
    if (isOccupied(nx, ny) || foods.some(f => f.x === nx && f.y === ny)) {
        return spawnOneFood(retryCount + 1);
    }

    // Tiered Distance Rule
    let minRadius = FOOD_TYPES[type].minRadius;
    
    // Fallback strategy
    if (retryCount > 10) minRadius = Math.floor(minRadius / 2);
    if (retryCount > 15) minRadius = 0;

    const head = snakes[0].body[0];
    const dist = Math.abs(head.x - nx) + Math.abs(head.y - ny);

    if (dist < minRadius) {
        return spawnOneFood(retryCount + 1);
    }

    const newFood = {
        x: nx,
        y: ny,
        type: type,
        expires: Date.now() + FOOD_TYPES[type].duration
    };
    foods.push(newFood);
}
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: implement tiered distance spawning with fallback"
```

---

### Task 3: Final Verification

**Files:**
- Manual test in browser.

- [ ] **Step 1: Verify Legendary apple spawn** (Observe distance and duration - 1.4s is very fast)
- [ ] **Step 2: Verify Normal apple spawn** (Observe 15+ tile distance)
- [ ] **Step 3: Verify overall game flow** (Ensure food continues to spawn even as map fills up)
