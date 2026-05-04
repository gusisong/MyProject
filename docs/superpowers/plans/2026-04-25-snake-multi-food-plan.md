# 多级苹果与动态规划 AI 贪吃蛇实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 移除分裂机制，实现多苹果系统、限时消失机制及动态规划 AI。

**Architecture:** 
- 将 `food` 改为 `foods` 数组。
- 新增苹果等级配置。
- 重写寻路逻辑为最优目标选择。
- 移除分裂逻辑。

---

### Task 1: 数据结构重构与移除分裂逻辑

- [ ] **Step 1: 移除 `splitSnake` 相关代码**
- [ ] **Step 2: 将 `food` 变量改为 `foods` 数组**
- [ ] **Step 3: 定义苹果等级配置对象 `FOOD_TYPES`**

### Task 2: 多苹果与限时机制实现

- [ ] **Step 1: 重写 `spawnFood` 以支持生成多个苹果**
- [ ] **Step 2: 实现 `updateFoods` 检查过期苹果并自动补全**
- [ ] **Step 3: 修改 `draw` 函数渲染不同颜色的苹果及预警闪烁**

### Task 3: 动态规划 AI 寻路

- [ ] **Step 1: 修改 `getPath` 逻辑，遍历 `foods` 计算 Utility Score**
- [ ] **Step 2: 实现自动剔除无法及时到达的苹果**
- [ ] **Step 3: 适配 `moveSnake` 处理吃到特定等级苹果的得分加成**

### Task 4: 最终测试与合并到 master

- [ ] **Step 1: 验证 AI 是否能正确避开障碍并优先吃高分苹果**
- [ ] **Step 2: 验证苹果消失预警效果**
- [ ] **Step 3: 合并代码到 master 分支并推送**
