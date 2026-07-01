# References — Harness Engineering 调研

> 本目录归档对"如何设计 harness"有长期价值的外部资料与方法论提炼。
> 一手来源：OpenAI、Anthropic、Stripe、LangChain、Mitchell Hashimoto 等团队的公开实践（2026）。
> 抓取/整理日期：2026-06-24。外部内容会变，引用请以原文为准。

## 索引
| 文件 | 主题 | 摘要 |
|------|------|------|
| 本文件 | Harness Engineering 方法论 | `Agent = Model + Harness` 与十大实践提炼 |
| `openspec-comparison.md` | OpenSpec 设计调研与对比 | 同类框架 OpenSpec 的三态分离/Delta/三层验证等设计，及可吸取的 5 条改进经验 |

---

## 0. 核心范式：`Agent = Model + Harness`

- 该公式由 LangChain 的 Vivek Trivedy 于 2026 年提出并迅速成为行业共识。
- 关键实证（"模型不变，只改 harness"）：
  | 来源 | 提升 | 改了什么 |
  |------|------|---------|
  | LangChain | 52.8% → 66.5%（+13.7 分） | 仅改 harness |
  | Can.ac | 6.7% → 68.3% | 仅改编辑接口格式 |
  | OpenAI Codex | 5 个月 / 100 万行 / ~1500 PR / 零手写代码 | 3 人起步 |
  | Stripe Minions | 每周 1300+ PR 无人值守合并 | — |
- **启示**：模型我们控制不了，harness 是可掌控、可复利的杠杆。这正是 harness-over-harness 存在的理由。

---

## 1. Harness Engineering 十大实践（提炼）

| # | 实践 | 一句话 | 在本仓库的落地 |
|---|------|--------|---------------|
| 1 | AGENTS.md = 文档免疫系统 | 每犯一次错就加一条规则 | `AGENTS.md` 第 5 节"每条规则对应真实教训" |
| 2 | 架构约束机械化执行 | 用 linter/CI 强制规则 | 决策原则 #3"机械化约束 > 口头约束" |
| 3 | 让应用对 agent 可见 | agent 要能"看到"运行结果 | SOP 阶段 0 探查可运行命令 |
| 4 | 闭环自验证 | 对照需求验证，而非自查代码 | SOP 阶段 3 + 决策原则 #2 |
| 5 | 会话间记忆 | 解决换班失忆 | 双轨记忆 `TODO.md`/`LOG.md` |
| 6 | 熵治理 | 对抗 AI slop，趋向简单 | 决策原则 #4 |
| 7 | 并行管理 | "等待很贵，纠正很便宜" | `AGENTS.md` 第 3 节 |
| 8 | Blueprint 模式 | 确定性与概率性节点交替 | SOP 分阶段 + 退出条件 |
| 9 | Trace 驱动迭代 | harness 不靠感觉，靠数据 | LOG 记录踩坑回流 |
| 10 | 渐进式采纳 | Hashimoto 六步法 | 从 MVP（AGENTS/TODO/LOG）起步 |

---

## 2. AGENTS.md 怎么写（综合 OpenAI / Hashimoto / GitHub 2500+ 仓库分析）

- **渐进式披露**：别写巨型单文件（上下文拥挤、过度指导、即时腐烂）。AGENTS.md 应是 ~100 行的"目录/路由"，指向深层文档。OpenAI 仓库有 **88 个** AGENTS.md，每个子系统一个。
- **每行对应一个真实过去错误**（Hashimoto / Ghostty）：例如"用 `zig build test` 而不是 `zig test`""不要改 build.zig 除非明确要求"。
- **命令优先、代码示例优于文字**，覆盖六大领域：**命令 / 测试 / 项目结构 / 代码风格 / git 工作流 / 边界**。

---

## 3. 会话间记忆：Anthropic 双体架构

- **初始化 Agent（仅首次）**：生成功能列表（JSON，每项含测试步骤、初始 `passes:false`）、`init.sh`、进度文件、初始化 git。
- **编码 Agent（后续每次）标准启动序列**：
  1. `pwd` 看目录 → 2. 读 git log + 进度文件 → 3. 读功能列表选**一个**最高优先级未完成项 → 4. 起开发服务器 → 5. 跑端到端测试确认没坏 → 6. 才开始实现。
- **JSON 优于 Markdown 存功能列表**：结构化数据 agent 不易误改；用强措辞保护（"删除/编辑测试是不可接受的"）。
  > 本仓库取舍：为兼顾人类可读，记忆用 Markdown，但通过红线 R2 + 模板强措辞达到同等"防误改"效果；目标项目若需强约束可改用 JSON 功能列表。

---

## 4. 多 Agent：Subagent / Team / Skill 的边界

- **Skill 管规则，Subagent/Team 管干活。**
- **Subagent**：当前会话分身，独立上下文，只回摘要 → 省 token；适合独立、结果导向、海量输出（如跑测试）。
- **Agent Team**：多个独立实例，可互发消息、共享任务列表 → 贵；适合并行审查、竞争假说调试、跨模块开发；建议 3–5 人。
- 详见本仓库 [`../GUIDE/orchestration.md`](../GUIDE/orchestration.md)。

---

## 5. 尚未解决（值得持续关注）

- **行为正确性**：结构质量可解，产品行为对不对仍难自动验证。
- **置信度校准**：agent 对正确与幻觉输出表现同等自信。
- **棕地项目**：成功案例多为绿地；老项目改造仍是难点。
- **harness 应趋向简化**：Manus 半年重写 5 次，方向是简化；持续变复杂往往是过度工程化信号。

---

## 来源链接（抓取于 2026-06-24，时效自负）

- Harness Engineering 实践指南：https://edison-a-n.github.io/2026/03/14/harness-engineering-practical-guide/
- Subagents vs Agent Teams 怎么选（少数派）：https://sspai.com/post/108489
- AGENTS.md 完整指南：https://vibecoding.app/blog/zh/agents-md-wanzheng-zhinan
- Agent Harness 架构深度解读：https://dranixj.com/articles/agent-harness-architecture-2026/

> TODO：补充对具体开源 harness 仓库（实际目录树）的实地调研，目前以方法论提炼为主。
