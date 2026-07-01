# LOG — harness-over-harness

> 人类可读的工作日志。记录**决策、缘由、踩坑、教训**。仅追加，倒序（新在上）。

---

### 2026-06-30 · 重组目录：本仓库内容物收纳进 `meta/`
- **做了什么**：把本仓库自身的记忆与素材统一移入新目录 `meta/`（`git mv` 保留历史）：`TODO.md` / `LOG.md`（本文件）/ `ideas.md` / `GUIDE/` / `templates/` / `references/`。根目录现仅保留 `README.md`（入口）、`AGENTS.md`（总指挥）、`CODEBUDDY.md`（IDE 约定）。同步更新所有交叉引用：
  - `README.md`：改写第 0 节介绍（新增"目录组织"说明）、重画第 2 节结构图、更新第 1/3 节全部链接到 `meta/`。
  - `AGENTS.md`：顶部新增「📁 目录约定」声明（下文简称均指 `meta/` 内）；§7 链接与 §8 启动指令路径改到 `meta/`。
  - `meta/GUIDE/orchestration.md`：指向根 `AGENTS.md` 的相对链接 `../AGENTS.md` → `../../AGENTS.md`。
  - `meta/templates/command.template.md`、`skill.SKILL.template.md`：示例路径加 `meta/` 前缀。
- **为什么这么做**：用户反馈文档散落根目录干扰视线。约定"根目录只放需被 agent 工具在顶层自动发现的约定文件（`AGENTS.md`/`CODEBUDDY.md`）+ 人类入口 `README.md`"，其余内容物收进 `meta/`，让根目录一眼看清"入口在哪、约束在哪"。
- **关键决策**：
  - 目录名取 `meta/`（语义=meta-harness 本体），简短可见、贴合主题。
  - 用 `git mv` 而非 `mv`，保留文件历史；纯位置迁移、内容零改动，符合红线 R2（不删除/不重写历史条目）。
  - 此任务各文件引用强依赖同一新路径、需保持一致，不属 §5.4 的"无依赖模块"，且未走 spec 落档流程 → 按 §5.4 例外由主 agent 串行执行，不拆 subagent。
  - `meta/GUIDE/harness-construction.md`、`meta/references/README.md` 的相对链接随目录整体平移后目标不变，无需修改。
- **踩到的坑 / 教训**：移动后最易断的是"跨目录相对链接"——只有 `orchestration.md` 的 `../AGENTS.md`（指向仍留在根的文件）需要多退一级；其余引用因目标与源同时迁入 `meta/` 而保持有效。重组目录时，**先分类"目标是否一起移动"再决定改不改链接**。
- **遗留问题 / 下一步**：若未来要让 Claude Code 用户也能在根目录发现入口，可补一个 `CLAUDE.md` 指向 `AGENTS.md`（本次未擅自新建）。`TODO.md` 中涉及路径的条目若有，后续按需校正。

---

### 2026-06-30 · 新增 §5.4 任务模块化与并行 subagent 规范
- **做了什么**：在 `AGENTS.md` 第 5 节末尾新增 §5.4"任务模块化与并行 subagent 规范"；在 §8 SOP 插入新步骤 5"模块化拆分 + 派 subagent"，把原步骤 5"干活"重写为步骤 6"执行（subagent 并行 / 主 agent 监控）"，SOP 编号顺延；同时把 §5 标题从"代码规范"扩为"代码与行为规范"以涵盖演示/任务拆分。
- **为什么这么做**：用户要求在 spec 落档后、开始执行前，强制把任务拆为**无依赖的模块**分给不同 subagent 并行推进；主 agent **只做调度与汇总**，避免单 agent 串行挤占并行性、避免主 agent 亲自写实现导致上下文爆炸。
- **关键约束**：
  - spec **必须先落档**才能派发（无基线 = subagent 易跑偏）。
  - 模块**无依赖**：禁数据 / 调用 / 共享状态依赖；只共享 spec 输入与最终汇总点。
  - **不写同一文件**（与 §3、orchestration.md 一致）。
  - **主 agent 不亲自写实现**——只调度派发 + 进度监控 + 结果汇总 + 闭环自检。
  - 粒度经验值：每模块 1–3 轮 subagent 交互可完成。
- **踩到的坑 / 教训**：一度想放进 §4 红线，但本规则是"按触发条件执行"而非"禁止某行为"，放 §4 会过严；放 §5 规范层加 **必须** 同样有约束力，且便于表达例外（极轻量改动不拆）。
- **遗留问题 / 下一步**：spec 落档的"约定位置"目前散见 `references/`、`docs/specs/`、`openspec/specs/`，需统一约定；§5 标题从"代码规范"扩为"代码与行为规范"以涵盖演示 / 任务拆分，待后续视情况再调。

---

### 2026-06-30 · 新增 §5.3 演示与可视化规范
- **做了什么**：在 `AGENTS.md` 第 5 节末尾新增 §5.3"演示与可视化规范"：规定向用户展示项目进展 / idea / spec / 架构图 / 流程图 / 状态机 / 对比表时，**默认使用 HTML + 丰富的 SVG**，并明确触发条件、HTML 容器要求、SVG 手绘要求、与 Markdown 的搭配关系。
- **为什么这么做**：用户希望 agent 汇报进展、介绍 idea 与 spec 时能更直观、可视化、信息密度高，而不是大段纯文字或简陋 ASCII。Markdown 表格 + 文字适合"细节 + 命令"，但"全局视图 + 关系 + 流程"必须交给手绘 SVG。
- **关键约束**：
  - HTML 文件落到 artifact 目录（`<appDataDir>/brain/<conversation-id>/`），用 `preview_url` 预览（不走 `open_result_view`）。
  - SVG **手画优先**，禁止"方块+箭头"极简图敷衍；颜色 / 形状 / 连线都要传递信息。
  - 复杂说明仍保留 Markdown，HTML/SVG 与 Markdown 搭配使用而非替代。
- **踩到的坑 / 教训**：在写规则时一度想塞进 §4 红线，但用户用词是"尽量"（偏好）而非"必须"（禁止），所以放在 §5 规范层而非 §4 硬约束，避免误伤轻量场景。
- **遗留问题 / 下一步**：暂无。等真实场景跑过一次后回流，看规则是否过严 / 过松 / 缺漏。

---

### 2026-06-24 · 调研 OpenSpec 并提炼改进经验
- **做了什么**：调研 Fission-AI/OpenSpec（SDD 框架，~34.9k★），产出 `references/openspec-comparison.md`；把可落地改进登记进 `ideas.md` 与 `TODO.md`，更新 `references/README.md` 索引。
- **为什么这么做**：用户希望吸取同类设计经验提升本仓库。判定 OpenSpec 与本仓库**同赛道不同层**（它是单项目 SDD 执行框架，我们是跨项目元 harness 生成器），不冲突可互补。
- **关键结论（5 条经验）**：
  1. 🔴 三态分离记忆（specs/changes/archive 物理隔离）→ 改进 TODO 模板分区。
  2. 🔴 Delta 增量式变更（ADDED/MODIFIED/REMOVED/RENAMED）→ LOG 模板加 delta 栏位。
  3. 🔴 机械化质量门（OpenSpec 三层验证 + 量化阈值）→ 给"harness 自检脚本"提优先级。
  4. 🟡 双档位工作流（Core/Extended）→ SOP 顶部加档位选择。
  5. 🟡 可验证写法（GIVEN-WHEN-THEN/SHALL）→ AGENTS 模板提倡规则可验证。
  - 不采纳其 22+ 工具适配器层（我们是方法论仓库，复用 AGENTS.md 事实标准即可）。
- **踩到的坑 / 教训**：OpenSpec 的 `AGENTS.md`/`project.md` 在多篇二手文章中描述不一致，已以 GitHub/DeepWiki 一手结构为准，避免被二手信息误导。
- **遗留问题 / 下一步**：上述 5 条改进已进 TODO，待后续实施（尚未改动 GUIDE/templates 本体，仅记录调研与计划）。

---

### 2026-06-24 · 初始化元 harness 仓库
- **做了什么**：从零搭起本仓库的骨架。确立"本仓库 = 生产 harness 的 harness（元装备）"这一定位，产出：
  - `README.md`（人类入口 + agent 启动指令）
  - `AGENTS.md`（构建者 agent 的总指挥：背景/技术栈/计算资源/🚨红线/代码规范/决策原则/编排路由/工作流）
  - `GUIDE/harness-construction.md`（为目标项目从 0 搭 harness 的 6 阶段 SOP）
  - `GUIDE/orchestration.md`（subagent/team/skill/command 的决策树与判据）
  - `templates/`（AGENTS / TODO / LOG / ideas / references / skill / command 共 7 个模板）
  - `TODO.md` + `LOG.md` + `ideas.md`（本仓库自身的双轨记忆）
  - `references/README.md`（业界 harness engineering 方法论提炼）
- **为什么这么做**：
  - 采用业界 `Agent = Model + Harness` 范式，对齐 OpenAI/Anthropic/Stripe/LangChain 的 Harness Engineering 实践。
  - 关键设计：**渐进式披露**（AGENTS.md 当路由表，细节下沉）、**双轨记忆**（TODO 给机器/LOG 给人）、**文档即免疫系统**（犯错就加规则）、**闭环自验证**（对照需求而非自查代码）、**熵治理**（趋向简单）。
  - 把"红线"做成最高优先级且覆盖临时指令，确保安全默认。
- **踩到的坑 / 教训**：（首次搭建，暂无）
- **遗留问题 / 下一步**：
  - `references/` 目前是方法论提炼，缺少对**具体开源 harness 仓库结构**的实地调研，已记入 TODO。
  - SOP 与模板尚未经过真实项目检验，首次实战后应回流改进（self-evolving）。

---
<!-- 维护提示：结构化的"做到哪了"在 TODO.md；本文件专注"为什么/怎么回事"。 -->
