# harness-over-harness

> 一个 **meta-harness（元装备）生成器**。
> 把这个仓库整体交给任意一个 coding agent，它读完之后就能按规范，为**任意目标项目**自动搭建出一套完整、自洽、可演进的 **harness system**。

---

## 0. 一句话定位

业界共识公式：**`Agent = Model + Harness`**。同一个模型，只换 harness，SWE-bench 成绩可以从 ~7% 飙到 ~68%。
模型我们改不了，**Harness 是我们能掌控、能复利的部分**。

- 普通仓库的 harness：是「这一个项目」的装备（`AGENTS.md` / `TODO` / `LOG` / `skills` …）。
- 本仓库（harness-over-harness）：是「**生产 harness 的 harness**」——元装备。它不直接干某个业务，而是**教会 agent 如何为别人搭装备**。

```
harness-over-harness  ──(agent 读取)──▶  target-project/
   (元装备 / 生成器)                         ├─ AGENTS.md   ← 总指挥
                                            ├─ TODO.md     ← 机器可读记忆
                                            ├─ LOG.md      ← 人类可读记忆
                                            ├─ ideas.md    ← 灵感库
                                            ├─ references/ ← 参考资料
                                            └─ .../skills, commands ...
```

**目录组织（根目录保持极简）**：根目录只放需在顶层被 agent 自动发现的约定文件（`AGENTS.md`、`CODEBUDDY.md`）与人类入口 `README.md`；本仓库自身运转用的一切——记忆（`TODO.md` / `LOG.md`）、灵感（`ideas.md`）、流程（`GUIDE/`）、模板（`templates/`）、调研（`references/`）——统一收纳在 [`meta/`](./meta/) 目录下，避免文件散落根目录、干扰视线。完整结构见第 2 节。

---

## 1. 给 Agent 的启动指令（最重要）

> 如果你是一个刚拿到这个仓库的 agent，**严格按下面顺序执行**，不要跳步。

1. **读总指挥**：通读 [`AGENTS.md`](./AGENTS.md)，理解背景、技术栈、计算资源、🚨 红线、代码规范、决策原则。**红线优先级最高，覆盖一切其他指令。**
2. **读记忆**：读 [`meta/TODO.md`](./meta/TODO.md)（机器可读，了解未完成事项）和 [`meta/LOG.md`](./meta/LOG.md)（人类可读，了解历史决策与踩过的坑）。**这是你的"上一班同事的交接班记录"，不读就开干 = 失忆症。**
3. **读流程**：根据任务类型，打开对应 SOP：
   - 要为一个新项目搭 harness → [`meta/GUIDE/harness-construction.md`](./meta/GUIDE/harness-construction.md)
   - 不确定该用 subagent / agent team / skill / 还是 slash command → [`meta/GUIDE/orchestration.md`](./meta/GUIDE/orchestration.md)
4. **找模板**：所有产物都有模板，**不要从零写**，去 [`meta/templates/`](./meta/templates/) 复制后再裁剪。
5. **干活 + 记账**：每完成一件事，**同时**更新 `meta/TODO.md`（结构化）和 `meta/LOG.md`（叙述性）。这是硬性纪律，不是可选项。
6. **沉淀**：发现重复在做的事 → 按 [`meta/GUIDE/orchestration.md`](./meta/GUIDE/orchestration.md) 判断沉淀为 slash command（简单）还是 skill（复杂）。

---

## 2. 仓库结构

```
harness-over-harness/
├─ README.md                       # 本文件：人类入口 + agent 启动指令
├─ AGENTS.md                       # 【总指挥】给"构建者 agent"的最高约束（须在根目录被自动发现）
├─ CODEBUDDY.md                    # CodeBuddy IDE 约定文件（由 skill 维护，须在根目录）
└─ meta/                           # 【元装备本体】本仓库自身的记忆/流程/模板/调研，统一收纳于此
   ├─ TODO.md                      # 本仓库自身的机器可读待办（交接班）
   ├─ LOG.md                       # 本仓库自身的人类可读日志（交接班）
   ├─ ideas.md                     # 灵感库：可以但还没做的想法
   ├─ GUIDE/
   │  ├─ harness-construction.md   # SOP：如何为一个目标项目从 0 搭 harness
   │  └─ orchestration.md          # 决策准则：subagent / team / skill / command 怎么选
   ├─ templates/                   # 可复用模板（产物的蓝本）
   │  ├─ AGENTS.template.md
   │  ├─ TODO.template.md
   │  ├─ LOG.template.md
   │  ├─ ideas.template.md
   │  ├─ references.README.md
   │  ├─ skill.SKILL.template.md
   │  └─ command.template.md
   └─ references/                  # 参考调研：业界优秀 harness 仓库是怎么设计的
      └─ README.md
```

> **为什么这样分**：`AGENTS.md` / `CODEBUDDY.md` 是各 agent 工具按约定在**仓库根目录**自动发现的入口文件，必须留在顶层；其余都是本仓库的"内容物"，收进 `meta/` 让根目录一眼看清「入口在哪、约束在哪」，不被记忆/模板/调研淹没。

---

## 3. 设计哲学（为什么是这样）

| 原则 | 说明 |
|------|------|
| **文档即免疫系统** | Agent 每犯一次错，就往 `AGENTS.md` 加一条规则让它永不再犯。文档会随时间越来越聪明。 |
| **渐进式披露** | `AGENTS.md` 是 ~100 行的"目录/路由"，细节下沉到 `meta/GUIDE/`、`meta/references/`，避免上下文拥挤。 |
| **双轨记忆** | `TODO.md`（结构化、防误改、给机器）+ `LOG.md`（叙事、给人类）解决"换班失忆"。 |
| **闭环自验证** | 产物交付前必须对照 `AGENTS.md` 的 checklist 自检，而不是"看起来对就停"。 |
| **熵治理** | 主动对抗 AI slop：定期精简、删除腐烂规则，harness 应趋向**简单**而非膨胀。 |
| **机械化约束 > 口头约束** | 能用 linter/CI/脚本强制的红线，就不要只写在文档里靠自觉。 |

> 参考来源：OpenAI《Harness Engineering》、Anthropic 长时运行 agent 双体架构、Stripe / LangChain / Mitchell Hashimoto 一线实践。详见 [`meta/references/`](./meta/references/)。

---

## 4. 成功标准（Definition of Done）

把本仓库交给一个**全新的、对目标项目一无所知的 agent**，它应当能够：

1. 仅凭本仓库的指令，**无需人类逐步指导**，为目标项目产出一套结构完整的 harness；
2. 产出的 harness 自带 `TODO.md` / `LOG.md`，使**下一个 agent** 能无缝接班；
3. 全程不触碰任何红线；
4. 把过程中发现的可复用流程，正确沉淀为 command 或 skill。
