# Coding Agent 研发工程师 · 面试突击刷题清单 v3

> 目标：Moonshot（月之暗面 / Kimi）Coding Agent 研发工程师，兼顾其他同类岗位。
> 更新时间：2026-08-17。v3 在 v2 基础上，根据"你个人想往底层(B)发展、但 DL 基础尚浅、且岗位大概率偏应用(A)"这一实际情况，**明确了本次面试的打法**。
> **阅读标记**：🟢 先看（建立直觉）｜🟡 再看（补细节、应对追问）｜🔴 深挖/权威原文（较难，有余力再看）。
> 想真正转底层的系统路径见配套文档：**《大模型底层长期学习路线图.md》**。

---

## 🎯 本次面试的核心打法（v3 定调，务必先读）

**结论：这次面试 = A（Agent 应用）当主线拿 offer，B（底层/推理）只做"直觉档"防卡壳，并把"想往底层"讲成加分故事。**

为什么这样定（三条理由）：
1. **岗位画风大概率是 A**："Coding Agent 研发"业界通常是 Agent 应用/框架方向；你想考 B 是"个人志向"，不是岗位要求。为一个大概率考 A 的岗全押 B，会白准备。
2. **B 几天补不到面试深度**：你 DL 基础是"入门过一点"，而 B 的硬核题（估算 MHA 计算量、FlashAttention、PagedAttention 推导）是 ML/系统科班的主场，几天突击必被打穿。
3. **A 是你唯一的主场**：简历（Claude Code、MCP、三层记忆、检索压缩、测试出身）通篇是 A 的信号，面试官会顺着它深挖——这是你能拿分的地方。

> ⚠️ 想往底层发展是好事，但那是**面试之后**的系统工程（见路线图文档）。**先靠 A 上岸，再在岗位上/业余转 B**，比现在裸押 B 明智得多。

### 三档投入分配
- **A（第三、四章）= 主线，70% 精力**：讲到能被追问三层不倒。
- **B（第五章）= 直觉档，20% 精力**：每个点只求"能讲直觉 + 一句话结论 + 不卡死"，**不追求推导**。
- **算法 + 八股（第一、二章）= 门槛，10% 精力**：巩固手生题型即可。

---

## 🗣️ 把"想往底层"讲成加分故事（面试话术，重点准备）

面试官问到你不熟的 B 类问题、或问"你的职业规划"时，别硬撑推导，用下面这套**把短板转成潜力信号**的话术（提前练熟，用自己的话说）：

**核心叙事**：
> "我是从**应用侧**深度用 Agent 进来的——大量实战 Claude Code、MCP、记忆机制、上下文压缩，对 Agent 在真实场景里怎么失败、怎么调优有第一手体感。同时我很清楚自己想往**底层**走：我从零训过 nanoGPT、复刻过 Letta 的记忆机制，就是想理解表象下面的原理。我的路径是**先用应用侧的工程能力和测试严谨度把事情做扎实，再逐步深入到模型和推理层**。"

**被问到具体 B 硬核题、答不出全部时**（诚实 + 展示学习力，别装懂）：
> "这个我目前只理解到直觉层面——比如 PagedAttention 我知道它是借鉴操作系统分页、把 KV Cache 分块管理来减少显存碎片；但更细的实现我还没吃透，这正是我接下来想系统补的方向。"
（**答对直觉 + 坦承边界 + 表明在学**，比瞎编强十倍。面试官最烦不懂装懂。）

**为什么这套有效**：面试官招应届/初级，看的是**潜力和学习力**，不是你现在啥都会。一个"应用扎实 + 有底层好奇心 + 诚实"的候选人，比一个"硬答一道推导题结结巴巴"的候选人得分高。

---

## 一、算法题（门槛，2 天内巩固手生题型）

难度多在 LeetCode Easy–Medium。优先级（按频率）：

1. **字符串 + 滑动窗口**：最长无重复子串、最小覆盖子串（贴近处理 prompt / context 截断）。
2. **树的遍历 / 构建**：前中后序、层序、路径和（呼应 code-review-graph 的 Tree-sitter 建图）。
3. **图 / BFS / DFS**：岛屿数量、课程表（拓扑排序）。
4. **哈希 + 双指针 + LRU 缓存**：LRU 务必练熟（常包装成"设计 context 缓存"，能顺势讲 KV cache / prompt caching）。
5. **基础 DP**：爬楼梯、背包选 1–2 道打底。

做法：每类 2–3 道原题，**Python 手写、不用补全**，计时 20–30 分钟，练"不卡壳"。

**📚 资源**：🟢 [代码随想录](https://programmercarl.com/)｜🟢 [LeetCode 热题 HOT 100](https://leetcode.cn/studyplan/top-100-liked/)

---

## 二、传统八股（门槛，0.5 天过一遍，低优先级）

Agent 应用岗出现频率中等偏低，只过高频点：
- **OS**：进程 vs 线程、死锁四条件、内存管理基础
- **DB**：B+ 树索引、事务隔离级别（**结合你 SQLite/FTS5 实践讲**）
- **网络**：HTTP vs HTTPS、TCP 三次握手

---

## 三、Agent / LLM 应用专项（★主线，70% 精力）

> 把每个点讲透、能被追问三层不倒。说不清的地方就是要突击的地方。

### 1. Agent Loop / Tool Use
- Function Calling 底层协议（JSON schema、多轮）；工具失败怎么恢复（**必被追问**）；ReAct / Plan-and-Execute / Reflection 区别；**死循环怎么办**（最大步数 / 重复检测 / 超时）。
- 🟢 [Anthropic《Building Effective Agents》](https://www.anthropic.com/engineering/building-effective-agents)（本岗最该读）｜🔴 [ReAct 论文 arXiv:2210.03629](https://arxiv.org/abs/2210.03629)

### 2. Context Engineering（强项，务必讲透）
- 为什么压缩上下文；四套知识库怎么检索、避免整库读入；code-review-graph 的 Tree-sitter 建图 + 按需检索**怎么实现**（能画数据流图）；Prompt caching 原理。
- 🟢 [Anthropic · Prompt Caching 文档](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)｜🟡 [Anthropic《Effective Context Engineering》](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

### 3. MCP
- 解决什么问题；**MCP vs Function Call 本质区别**；Host/Client/Server、Resource/Tool/Prompt；"接 10 个工具选谁"。
- 🟢 [MCP 中文入门（知乎）](https://zhuanlan.zhihu.com/p/16948315081)｜🔴 [MCP 官方文档](https://modelcontextprotocol.io/introduction)

### 4. Memory
- Letta 三层记忆怎么运作；"记忆增强 ≠ 权重级持续学习"（in-context vs fine-tuning）；跨会话记忆；上下文放不下怎么办。
- 🟢 [Letta 官方文档](https://docs.letta.com/)｜🔴 [MemGPT 论文 arXiv:2310.08560](https://arxiv.org/abs/2310.08560)

### 5. Evaluation（★你的差异化优势区）
- 评测维度：任务成功率、多轮一致性、工具调用准确率、步数/token 成本、失败类型分布；怎么构造 benchmark + 自动打分。**结合测试背景发挥**。
- 🟡 [SWE-bench 官网](https://www.swebench.com/)（Coding Agent 主流基准，必知）

### 6. Agent vs Workflow（高频送分题）
- 区别、什么任务用哪个、为什么很多场景不该用 Agent、混合架构。
- 🟢 见 3.1 的 [Anthropic《Building Effective Agents》](https://www.anthropic.com/engineering/building-effective-agents)

### 7. Skills 与 A2A（Claude Code 主场 + 前沿）
- Skills vs Prompt/System Prompt/Few-shot；FC/MCP/Skills/A2A 四者关系；A2A 解决什么。
- 🟢 [Anthropic《Agent Skills》文档](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills)｜🟡 [A2A 协议官网](https://a2a-protocol.org/)

### 8. 安全性 & 可靠性（★又一优势区）
- Prompt Injection（直接/间接）与防御；"操作数据库怎么防误删"（最小权限 / Human-in-the-Loop）；生产四大坑（死循环/工具幻觉/上下文污染/token 爆炸）；幂等/回滚/超时/降级。
- 🟢 [OWASP LLM Top 10 实践解读](https://www.promptfoo.dev/docs/red-team/owasp-llm-top-10/)｜🟡 [OWASP GenAI 官方](https://genai.owasp.org/)

### 9. 框架选型 & 成本
- LangChain / LangGraph / CrewAI / AutoGen 差异；token 成本优化（动态加载 / 模型路由 / 压缩 / 缓存）。
- 🟢 [LangGraph 官方文档](https://langchain-ai.github.io/langgraph/)

### 10. Claude Code 源码级机制（★最强主场题）
- 主循环 Query；Compact 压缩；**为什么用 grep 而非 RAG**；多 Agent 子任务；CLAUDE.md。
- 🟡 [小林《图解 Claude Code》系列](https://xiaolincoding.com/other/ai.html)｜🟡 [字节 Trae Agent 源码](https://www.cnblogs.com/xiaoqi/p/18971235/Trae-Agent)

---

## 四、RAG 专项（★主线补齐，Agent 岗几乎必问）

- 完整链路：Chunking（切分 / overlap）→ Embedding（选型 / 维度）→ 向量库 → 检索 → **Rerank（粗排+精排 / Cross-Encoder）** → 生成。
- 向量检索 vs 关键词检索；Query 改写 / 多路召回；GraphRAG；**"RAG 和 Agent 什么关系？RAG 是不是 Agent 的一个工具？"**
- **把你的"四套本地知识库检索"用 RAG 术语重讲一遍**（简历项目挂钩 RAG 术语 = 巨大加分）。
- 🟢 [知乎《一文读懂大模型 RAG》](https://zhuanlan.zhihu.com/p/675509396)｜🟡 [Zilliz《RAG chunking 与评估》](https://zilliz.com.cn/blog/exploring-rag-chunking-llms-and-evaluations)｜🟡 [RAG 教程 GitHub](https://github.com/vivy-yi/rag-tutorial)

---

## 五、大模型底层 & 推理（本次面试只到"直觉档"🟢，20% 精力）

> **本次面试策略：每个点只求能讲直觉 + 一句话结论 + 不卡死，不追求推导。** 想真正吃透见《大模型底层长期学习路线图.md》。

**要能一句话讲清直觉的点**：
- **注意力**：自注意力在算什么；MHA → **MQA / GQA**（省 KV cache）；FlashAttention 快在"省显存搬运不是省计算"。
- **位置编码**：**RoPE**（旋转位置编码，主流）是给 token 加位置信息。
- **推理两阶段**：**prefill**（算力密集）/ **decode**（显存带宽密集）。
- **KV Cache**：为什么吃显存；**PagedAttention** = 借鉴 OS 分页、分块管理 KV Cache 减碎片。
- **对齐**：预训练 → SFT → **RLHF / DPO**（DPO 跳过 RL，直接用偏好数据，更稳更省）。

**📚 资源（直觉档够用）**
- 🟢 [图解 Transformer（中文·宝玉译）](https://baoyu.io/translations/llm/illustrated-transformer)
- 🟢 [阿里云《vLLM PagedAttention 原理详解》](https://developer.aliyun.com/article/1664805)
- 🟢 [知乎《人机对齐：从 RLHF 到 DPO》](https://zhuanlan.zhihu.com/p/10059528011)

---

## 六、Moonshot / Kimi 专项

- 核心卖点**长上下文**（K2 已到 256K、主打 Agentic Coding；K2 系列 2026-05 停维护，最新是 K3，面试前查当前主力模型）。
- 备一个观点：**"长上下文做到几百万字，还需要检索压缩吗？"** —— 长上下文 ≠ 免费（成本 / 延迟 / 注意力衰减"大海捞针"），检索压缩仍有价值，把这个论证讲清就是加分。
- 🟢 [Moonshot 官方 blog · Kimi K2 更新](https://platform.moonshot.cn/blog/posts/kimi-k2-0905)｜🟡 [知乎《回顾 Kimi 三年技术亮点》](https://zhuanlan.zhihu.com/p/2067902602281480917)

---

## 附录：面经题库（做完准备后自测）

- [卡码笔记 · Agent 大厂面试题汇总](https://notes.kamacoder.com/interview/llm/agent_interview.html)
- [卡码笔记 · 大模型面经总入口](https://notes.kamacoder.com/interview/llm/)
- [小林 coding · AI 大模型面试题合集（含 Claude Code 源码）](https://xiaolincoding.com/other/ai.html)
- [牛客 · 月之暗面「大模型 LLM 一面」真实面经](https://www.nowcoder.com/discuss/875874293186379776)

---

## 建议的几天分配（A 主线版）

- **第 1–2 天**：算法（字符串/树/图/哈希/LRU，每天 4–5 道）+ 每晚过八股高频点。
- **第 3 天**：第三章 Agent 应用逐条自查 + **第四章 RAG 补齐**（重点：知识库项目用 RAG 术语重讲）。
- **第 4 天**：第三章新增项（安全 / 可靠性 / Agent vs Workflow / Claude Code 源码）+ 第六章 Kimi + **练"底层热情故事"话术**。
- **第 5 天（余力）**：第五章底层，**只按直觉档 🟢 过一遍**，能一句话讲清即可。
- **最后半天**：模拟自我介绍 + 项目深挖（三层记忆、8.2× token 缩减、MCP vs FC、"你怎么评测 Agent"）+ 演练"被问底层答不出时"的诚实话术。

> **一句话**：这次靠 A 上岸，B 只防卡壳、当故事；想转底层，走配套那份长期路线图，别拿这次面试当赌注。
