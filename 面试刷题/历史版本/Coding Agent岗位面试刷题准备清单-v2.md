# Coding Agent 研发工程师 · 面试突击刷题清单 v2

> 目标：Moonshot（月之暗面 / Kimi）Coding Agent 研发工程师，兼顾其他同类岗位。
> 更新时间：2026-08-17。本版在 v1 基础上，依据多篇大厂面经补齐了缺口，并给每个知识点配了分层阅读链接。
> **阅读标记**：🟢 先看（建立直觉）｜🟡 再看（补细节、应对追问）｜🔴 深挖/权威原文（较难，有余力再看）。
> 建议顺序：同一知识点先 🟢 再 🟡，🔴 是被面试官追到底层时的底气，时间不够可跳过。

---

## ⚠️ 开工前必读：先判断岗位「画风」

面经显示，这类岗位实际分两种画风，考点差别很大：

| 画风 | 考什么 | 本清单章节 |
|---|---|---|
| **A. Agent 应用工程**（偏应用） | Agent 架构、Function Calling、MCP、RAG、记忆、系统设计、成本优化 | 第三、四章（主攻）|
| **B. 大模型底层 / 推理**（偏 infra） | Transformer、训练对齐、推理优化、vLLM、显存 | 第五章（条件性重点）|

**关键信号**：牛客上一篇[月之暗面「大模型 LLM 一面」真实面经](https://www.nowcoder.com/discuss/875874293186379776)问的全是 B 类——prefill/decode 两阶段、推理 OOM 排查、估算 MHA 计算量、vLLM PagedAttention、FlashAttention、RMSNorm vs LayerNorm。

**行动**：**务必先拿到目标岗的 JD**，看职责里更强调「Agent 应用 / 工程落地」还是「模型 / 推理 / 训练」。
- 偏 A → 主攻第三、四章，第五章只需了解概念。
- 偏 B 或不确定 → 第五章升为 P0，必须补。
- 没有 JD 的话，按「A 为主 + B 补概念」两头都摸一遍，防止踩空。

---

## 一、算法题（预计投入：2 天）

Coding Agent 研发岗一般不考难算法（不是纯算法岗），难度多在 LeetCode Easy–Medium，但基础手撕能力是门槛。

优先级（按面试出现频率）：

1. **字符串处理 + 滑动窗口**：最长无重复子串、最小覆盖子串——和"处理文本 / token"强相关，面试官爱问（贴近解析 prompt、context 截断）。
2. **树的遍历 / 构建**：前中后序、层序、路径和——和 code-review-graph"用 Tree-sitter 建代码结构图"呼应。
3. **图 / BFS / DFS**：岛屿数量、课程表（拓扑排序）——聊到 Agent 任务依赖 / 工具调用图时容易引出。
4. **哈希表 + 双指针**：两数之和变体、**LRU 缓存**（务必练熟，常被包装成"设计一个 context 缓存"，能顺势讲 KV cache / prompt caching 加分）。
5. **基础 DP**：爬楼梯、背包选 1–2 道打底即可。

做法：每类挑 2–3 道 LeetCode 原题，**用 Python 手写、别用自动补全**，计时 20–30 分钟一道，练"不卡壳"而非刷题量。

**📚 资源**
- 🟢 [代码随想录](https://programmercarl.com/)：按题型分类，讲解清晰，最适合突击巩固手生题型。
- 🟢 [LeetCode 热题 HOT 100](https://leetcode.cn/studyplan/top-100-liked/)：上面那些题型基本都在这个清单里。

---

## 二、系统设计 / 传统八股（预计投入：0.5–1 天，低优先级）

对 Agent 应用岗，传统 OS/网络/DB 八股出现频率**中等偏低**，只过一遍高频点，别深挖：

- **操作系统**：进程 vs 线程、死锁四条件、内存管理基础
- **数据库**：索引原理（B+ 树）、事务隔离级别——**结合你 SQLite / FTS5 实践讲**，比背概念有说服力
- **网络**：HTTP vs HTTPS、TCP 三次握手

这部分投入产出比低，硬核亮点在 Agent 部分，面试官大概率把时间花在深挖那里。

---

## 三、Agent / LLM 应用专项（重点，也是你的优势区）

> 不是刷题，而是把每个技术点讲透、能被追问三层不倒。说不清楚的地方就是要突击的地方。

### 1. Agent Loop / Tool Use 机制
自查：
- Function Calling 的底层协议（JSON schema、多轮工具调用）到底长什么样？
- 工具调用失败了 Agent 怎么恢复？（简历写了"失败恢复"，**必被追问**）
- ReAct、Plan-and-Execute、Reflection 三种范式的区别与适用场景？
- **Agent 陷入死循环怎么办？**（高频，答：最大步数 / 重复动作检测 / 超时控制）

**📚 资源**
- 🟢 [Anthropic《Building Effective Agents》](https://www.anthropic.com/engineering/building-effective-agents)：**本岗最该读的一篇**。Anthropic 官方讲 agent 设计模式，且权威地界定了 Agent vs Workflow（见下方 3.6）。
- 🟡 [ReAct 论文的通俗解读（Medium）](https://cbarkinozer.medium.com/react-synergising-reasoning-and-acting-in-language-models-79e09526ffbe)
- 🔴 [ReAct 原论文 arXiv:2210.03629](https://arxiv.org/abs/2210.03629)｜[官方代码库](https://github.com/ysymyth/ReAct)：Agentic LLM 的开山之作，被追问范式时的底气。

### 2. Context Engineering（强项，务必讲透）
自查：
- 为什么要做上下文压缩？四套本地知识库怎么检索、怎么避免"整库读入"？
- code-review-graph 用 Tree-sitter 建图 + 按需检索**怎么实现的**（务必能画数据流图）？
- Prompt caching 原理（Claude Code 重度用户必须懂）？

**📚 资源**
- 🟢 [Anthropic 官方 · Prompt Caching 文档](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)
- 🟡 [Anthropic《Effective Context Engineering for AI Agents》](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

### 3. MCP（Model Context Protocol）
自查：
- MCP 解决什么问题（相比传统 function calling）？
- **MCP 和 Function Call 的本质区别**？（高频追问）
- Host / Client / Server 三角色、Resource / Tool / Prompt 三原语的区别？
- "给团队接 10 个外部工具，你选 MCP 还是直接写 FC，为什么？"

**📚 资源**
- 🟢 [MCP 中文入门（知乎·深入体验 MCP）](https://zhuanlan.zhihu.com/p/16948315081)
- 🔴 [MCP 官方文档 Introduction](https://modelcontextprotocol.io/introduction)：架构、原语、通信（JSON-RPC 2.0）的权威来源。

### 4. Memory 机制
自查：
- Letta 三层记忆（Core / Recall / Archival）怎么运作？
- 为什么"记忆增强 ≠ 权重级持续学习"？（要能展开：in-context learning vs fine-tuning / continual learning 的区别）
- Agent 怎么实现**跨会话记忆**？上下文窗口放不下怎么办？（滑动窗口 / 摘要 / 重要性过滤）

**📚 资源**
- 🟢 [Letta（原 MemGPT）官方文档](https://docs.letta.com/)｜🔴 [MemGPT 论文 arXiv:2310.08560](https://arxiv.org/abs/2310.08560)（你复刻的原型，必看）

### 5. Evaluation（Agent 评测）—— 你的差异化优势区
自查（面经确实会问"怎么评测一个 Agent 好不好"）：
- 评测维度：任务成功率、多轮一致性、工具调用准确率、步数 / token 成本、失败类型分布怎么设计？
- 怎么构造 benchmark、怎么自动打分？
- **（结合你测试背景发挥：这是"测试出身"翻转成优势的地方，提前想好话术）**

**📚 资源**
- 🟡 [SWE-bench 官网](https://www.swebench.com/)：Coding Agent 领域最主流的评测基准，必知。
- 🟡 [Anthropic《Building Effective Agents》里的评测思路](https://www.anthropic.com/engineering/building-effective-agents)（同上文）

### 6. 【新增】Agent vs Workflow（高频送分题）
自查：
- Agent 和 Workflow 有什么区别？什么任务用哪个？
- 为什么很多场景**不该用 Agent 而该用 Workflow**（可控、可预测、便宜）？
- 混合架构在生产环境怎么落地？

**📚 资源**：见 3.1 的 [Anthropic《Building Effective Agents》](https://www.anthropic.com/engineering/building-effective-agents)——这篇就是讲这个的最佳答案。

### 7. 【新增】Skills 与 A2A（Claude Code 主场 + 前沿协议）
自查：
- Skills 是什么？和 Prompt / System Prompt / Few-shot 的区别？激活机制？
- Function Call / MCP / Skills / A2A **四者的关系**（能用一个统一比喻讲清楚）？
- A2A 协议解决 MCP 解决不了的什么问题（Agent 之间的协作）？

**📚 资源**
- 🟢 [Anthropic《Agent Skills》文档](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills)（你天天用 Claude Code，这是主场）
- 🟡 [A2A（Agent2Agent）协议官网](https://a2a-protocol.org/)

### 8. 【新增】安全性 & 可靠性（你测试背景的又一优势区）
自查：
- 什么是 **Prompt Injection**（直接 / 间接）？怎么防御？
- "Agent 要操作数据库，怎么保证不误删数据？"（最小权限、Human-in-the-Loop、确认环节）
- 生产环境四大坑：死循环、工具幻觉、上下文污染、token 爆炸——怎么约束？
- 可靠性设计：幂等性、回滚、超时、降级。

**📚 资源**
- 🟢 [OWASP LLM Top 10 实践解读（Promptfoo）](https://www.promptfoo.dev/docs/red-team/owasp-llm-top-10/)
- 🟡 [OWASP GenAI Security Project 官方](https://genai.owasp.org/)（Prompt Injection 连续两版排第一）

### 9. 【新增】框架选型 & 成本优化
自查：
- LangChain vs LangGraph vs CrewAI vs AutoGen：核心差异与适用场景？
- Agent token 消耗大怎么优化成本？（动态加载工具、模型路由、上下文压缩、prompt 缓存、选对工作模式）

**📚 资源**
- 🟢 [LangGraph 官方文档](https://langchain-ai.github.io/langgraph/)（图编排，生产级 Agent 主流选型之一）

### 10. 【新增】Claude Code 源码级机制（你的最强主场题）
你是 Claude Code 重度用户，面经里专门有"图解 Claude Code"系列，这是**最该拿满分的题**。自查：
- 主循环（Query 机制）怎么跑？
- 上下文压缩 Compact 怎么触发、怎么做？
- **为什么用 grep 检索代码而不用 RAG？**（经典追问）
- 多 Agent 子任务怎么划分？CLAUDE.md 的作用？

**📚 资源**
- 🟡 [小林 coding《图解 Claude Code》系列](https://xiaolincoding.com/other/ai.html)（12 篇源码 + 实战，含"为何用 grep 而非 RAG"）
- 🟡 [字节 Trae Agent 源码深入浅出](https://www.cnblogs.com/xiaoqi/p/18971235/Trae-Agent)（另一个开源 Coding Agent，对照着看能答"不同 coding agent 的设计取舍"）

---

## 四、【新增】RAG 专项（P0 缺口，Agent 岗几乎必问）

面经里 RAG 是独立三大板块之一，你 v1 只在 Context Engineering 里带了一句"检索"，需要系统补。自查：
- RAG 完整链路：Chunking（切分策略 / overlap）→ Embedding（选型 / 维度）→ 向量库 → 检索 → **Rerank（粗排 + 精排 / Cross-Encoder）** → 生成。
- **向量检索 vs 关键词检索** 各自优劣、什么时候混合？
- Query 改写、多路召回是干嘛的？
- GraphRAG / LightRAG 相比朴素 RAG 强在哪？
- **"RAG 和 Agent 什么关系？RAG 是不是 Agent 的一个工具？"**（高频，务必想清楚）
- 结合你项目：你的"四套本地知识库检索"其实就是一套朴素 RAG，能不能用 RAG 的术语重新讲一遍？（**能把简历项目和 RAG 术语挂钩，是巨大加分**）

**📚 资源**
- 🟢 [知乎《一文读懂大模型 RAG（含高级方法）》](https://zhuanlan.zhihu.com/p/675509396)：中文入门首选。
- 🟡 [Zilliz《RAG chunking 与评估的三大关键策略》](https://zilliz.com.cn/blog/exploring-rag-chunking-llms-and-evaluations)：切分 / rerank 讲得细。
- 🟡 [完整 RAG 技术教程（GitHub，含 Notebook 实战）](https://github.com/vivy-yi/rag-tutorial)：想动手时用。

---

## 五、【新增】大模型底层 & 推理（条件性重点：偏 B 画风则 P0）

> 若 JD 偏底层 / 推理 / 训练，或不确定，这章必补；若确认纯应用岗，只需了解概念、能接得住话即可。
> 这块内容量大、几天补不完，**策略是"每个点能讲清直觉 + 一句话结论"，不追求推导**。

### 5.1 Transformer 架构
- 自注意力 / 多头注意力（MHA）原理；MHA → **MQA / GQA** 的演进（省 KV cache）；FlashAttention 为什么快（不省计算省显存搬运）。
- 位置编码：绝对（sin/cos）vs **RoPE**（旋转位置编码，主流）vs ALiBi。
- 分词器（Tokenizer）原理；为什么 RMSNorm 比 LayerNorm 常见。

**📚 资源**
- 🟢 [图解 Transformer（中文·宝玉译）](https://baoyu.io/translations/llm/illustrated-transformer)｜🔴 [英文原文 Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)：入门自注意力最佳。
- 🟡 [李沐《动手学深度学习》· 注意力机制章节](https://zh.d2l.ai/)：系统、带代码，想扎实补底层用这个。

### 5.2 训练与对齐
- 大模型怎么训练出来的：预训练 → SFT → **对齐（RLHF / DPO）**。
- Scaling Law 与涌现能力；LoRA 等参数高效微调。
- RLHF vs DPO 的区别（DPO 跳过了 RL，直接用偏好数据优化，更稳更省）。

**📚 资源**
- 🟢 [知乎《人机对齐：从 RLHF 到 DPO》](https://zhuanlan.zhihu.com/p/10059528011)
- 🟡 [HuggingFace 中文《简化对齐：从 RLHF 到 DPO》](https://hugging-face.cn/blog/ariG23498/rlhf-to-dpo)

### 5.3 推理优化（Moonshot 真实一面重灾区）
- **prefill / decode 两阶段**分别关注什么（prefill 算力密集、decode 显存带宽密集）。
- **KV Cache** 是什么、为什么吃显存；**vLLM PagedAttention** 怎么像操作系统分页一样管 KV Cache 减少碎片。
- 推理 OOM 怎么排查；量化（INT8/INT4）、MoE、解码策略（温度 / TopP / TopK）。

**📚 资源**
- 🟢 [阿里云《vLLM 核心技术 PagedAttention 原理详解》](https://developer.aliyun.com/article/1664805)
- 🟡 [KV Cache 深度解析（知乎）](https://zhuanlan.zhihu.com/p/2016843212178882587)｜[July 博客·一文通透 vLLM 与 PagedAttention](https://blog.csdn.net/v_JULY_v/article/details/144218958)

---

## 六、Moonshot / Kimi 专项

- Kimi 的核心卖点是**长上下文**（K2 已到 256K，且主打 **Agentic Coding**；注意 K2 系列已于 2026-05 停止维护，最新是 K3，面试前查一下当前主力模型）。
- 提前想一个自己的观点：**"如果长上下文做到几百万字，Context Engineering 还需要做检索压缩吗？"**（这是结合公司技术方向的高频开放题，答案不是非黑即白：长上下文 ≠ 免费，仍有成本 / 延迟 / "大海捞针"注意力衰减问题，检索压缩仍有价值——把这个论证讲清楚就是加分）。

**📚 资源**
- 🟢 [Moonshot 官方 blog · Kimi K2 更新（更强代码能力）](https://platform.moonshot.cn/blog/posts/kimi-k2-0905)
- 🟡 [知乎《一文回顾 Kimi 这三年的技术亮点》](https://zhuanlan.zhihu.com/p/2067902602281480917)（面试前快速了解公司技术脉络）

---

## 附录：面经题库入口（做完准备后用来自测）

- [卡码笔记 · 2026 Agent 大厂面试题汇总](https://notes.kamacoder.com/interview/llm/agent_interview.html)（本清单缺口的主要来源，题目最全）
- [卡码笔记 · 大模型面经总入口](https://notes.kamacoder.com/interview/llm/)
- [小林 coding · AI 大模型面试题合集（74 题 / 含 Claude Code 源码）](https://xiaolincoding.com/other/ai.html)
- [牛客 · 月之暗面「大模型 LLM 一面」真实面经](https://www.nowcoder.com/discuss/875874293186379776)（判断画风的关键样本）

---

## 建议的几天分配（按"A 应用为主"排；若偏 B 则把第五章前置）

- **第 1–2 天**：算法（字符串 / 树 / 图 / 哈希 / LRU，每天 4–5 道）+ 每晚过一遍第二章八股高频点。
- **第 3 天**：第三章 Agent 应用逐条自查 + **第四章 RAG 补齐**（重点：把你的知识库项目用 RAG 术语重讲）。
- **第 4 天**：第三章新增项（安全 / 可靠性 / Agent vs Workflow / Claude Code 源码）+ 第六章 Kimi 动态。
- **第 5 天（若时间够 / 偏 B 画风）**：第五章底层 & 推理，按"能讲直觉"过一遍。
- **最后半天**：模拟自我介绍 + 项目深挖问答（三层记忆、8.2× token 缩减怎么来的、MCP vs FC、"你怎么评测一个 Agent"）。

> **核心策略不变**：算法只巩固手生题型；八股 / 底层求"能接住话"；**Agent 应用 + 你简历上的实践是主场，投入产出比最高**——面试官大概率顺着简历的 MCP、三层记忆、Agent Loop、检索压缩深挖，把这些讲到能被追问三层不倒，就赢了。
