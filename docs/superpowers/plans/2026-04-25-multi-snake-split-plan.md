# 多蛇分裂游戏实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 重构贪吃蛇为多蛇并行系统，加入随机分裂、命名和本地排名。

**Architecture:** 将 `snake` 改为 `snakes` 数组管理，重写碰撞检测为全局检测，新增 `Leaderboard` 模块。

**Tech Stack:** JavaScript, HTML5 Canvas.

---

### Task 1: 数据结构重构与命名系统

- [ ] **Step 1: 移除血量 UI，增加排名 UI 容器**
- [ ] **Step 2: 定义命名池与蛇对象结构**
- [ ] **Step 3: 重构初始化逻辑为多蛇支持**

### Task 2: 分裂机制实现

- [ ] **Step 1: 编写分裂逻辑函数 (截断与新蛇生成)**
- [ ] **Step 2: 在游戏循环中加入随机分裂触发器**
- [ ] **Step 3: 验证分裂后的长度损耗逻辑**

### Task 3: 全局碰撞检测与 AI 适配

- [ ] **Step 1: 修改 A* 避障逻辑，将所有蛇身视为障碍**
- [ ] **Step 2: 修改 `moveSnake` 为全局碰撞检测 (碰壁或撞到任何蛇即结束)**
- [ ] **Step 3: 适配 `findSurvivalMove` 到多蛇环境**

### Task 4: 排名系统与渲染抛光

- [ ] **Step 1: 实现内存排名列表 (记录得分)**
- [ ] **Step 2: 在蛇头上方渲染名字标签**
- [ ] **Step 3: 完善游戏结束后的排名展示**
