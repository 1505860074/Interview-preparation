# Coding Agent 研发工程师 · 面试突击刷题清单

目标：Moonshot（月之暗面 / Kimi）Coding Agent 研发工程师，兼顾其他可能投递的同类岗位。
背景：几天时间临近面试突击，两类考察重点（算法 + Agent/LLM 工程）都要覆盖。

## 整体思路

几天时间不可能系统刷完 LeetCode，也不可能补全 LLM 理论。策略是：算法只巩固大概率已经会但手生的中等题型，八股/Agent 部分重点讲清楚简历上写的东西到底是怎么运作的——面试官大概率会顺着简历上的 MCP、三层记忆、Agent Loop 这些点深挖，这是主场，投入产出比最高。

---

## 一、算法题（预计投入：2 天）

Coding Agent 研发岗一般不会考很难的算法（不是纯算法岗），但基础的手撕代码能力是门槛，考察难度多在 LeetCode Easy–Medium。

优先级排序（按面试出现频率）：

1. **字符串处理 + 滑动窗口**：最长无重复子串、最小覆盖子串——和"处理文本/token"强相关，Coding Agent 岗位面试官很爱问，贴近日常工作（解析 prompt、处理 context 截断）。
2. **树的遍历/构建**：二叉树前中后序、层序遍历、路径和——考察递归思维，和 code-review-graph 项目"用 Tree-sitter 建代码结构图"呼应，可能被追问树结构相关的算法应用。
3. **图/BFS/DFS 基础**：岛屿数量、课程表（拓扑排序）——如果聊到 Agent 的任务依赖/工具调用图，这类题容易被引出来。
4. **哈希表 + 双指针**：两数之和变体、LRU 缓存实现——LRU 特别推荐练熟，经常被包装成"设计题"（比如"设计一个 context 缓存"），能顺着讲到 LLM 的 KV cache / prompt caching 是加分项。
5. **基础动态规划**：爬楼梯、背包问题选 1-2 道打底即可，不用深挖。

建议：每类挑 2-3 道 LeetCode 原题，用 Python 手写（别用 IDE 自动补全），计时 20-30 分钟一道，练的是"面试时不卡壳"，不是刷题量。

---

## 二、系统设计/八股（预计投入：1 天）

传统的 OS/网络/数据库八股，问的概率中等，只过一遍高频考点，不深挖：

- **操作系统**：进程 vs 线程、死锁条件、内存管理基础
- **数据库**：索引原理（B+树）、事务隔离级别——结合 SQLite/FTS5 实践经验讲，比背概念更有说服力
- **网络**：HTTP vs HTTPS、TCP 三次握手——基础但常被问到

这部分不用精刷，更硬核的亮点在 Agent 实践，面试官大概率把大部分时间花在深挖那部分。

---

## 三、Agent/LLM 专项（预计投入：2-3 天，重点，也是优势区）

这部分不是刷题，而是把简历里每一个技术点都能讲透、能被追问三层不倒。按下面清单自查，说不清楚的地方就是要突击的地方：

### 1. Agent Loop / Tool Use 机制
- Function calling 的底层协议是什么样的（JSON schema、多轮工具调用）
- 工具调用失败了 Agent 怎么恢复？（简历提到"失败恢复"，一定会被追问）
- ReAct、Plan-and-Execute 等主流 Agent 范式的区别

### 2. Context Engineering（强项，务必讲透）
- 为什么要做上下文压缩？四套本地知识库怎么做检索、怎么避免"整库读入"？
- code-review-graph 的 8.2× token 缩减具体怎么实现的（Tree-sitter 建图 + 按需检索）——简历亮点，务必能画图讲清楚数据流
- Prompt caching 原理（结合 Anthropic 的 prompt caching 机制，简历提到 Claude Code 重度使用，这个必须懂）

### 3. MCP（Model Context Protocol）
- MCP 解决什么问题（相比传统 function calling）
- Server/Client 架构、Resource vs Tool vs Prompt 三种原语的区别

### 4. Memory 机制
- 复刻的 Letta 三层记忆（Core/Recall/Archival）具体怎么运作，为什么"记忆增强 ≠ 权重级持续学习"——这句话背后的原理要能展开讲（in-context learning vs fine-tuning/continual learning 的区别）

### 5. Evaluation（Agent 评测）
- 简历技能栏提到但项目里没具体展开的点，容易被追问"怎么评测一个 Agent 好不好"，提前想清楚：任务成功率、多轮一致性、工具调用准确率这些维度怎么设计

### 6. Moonshot/Kimi 相关
- 面试前看一下 Kimi 最近的公开技术分享（长上下文能力是 Kimi 的核心卖点），可能问"如果长上下文做到 200 万字，Context Engineering 还需要做检索压缩吗？"这种结合公司技术方向的问题，提前想一个自己的观点

---

## 建议的几天分配

- **第 1-2 天**：算法（字符串/树/图/哈希，每天 4-5 道）+ 每晚过一遍八股高频点
- **第 3 天**：把简历里 Agent 部分逐条自查，写不清楚的地方查资料补
- **第 4 天**：模拟自我介绍 + 项目深挖问答（重点：三层记忆、8.2× token 缩减怎么来的、MCP vs function calling），查 Kimi 最新技术动态
