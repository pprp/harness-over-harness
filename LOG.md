# LOG — harness-over-harness

> 人类可读的工作日志。记录**决策、缘由、踩坑、教训**。仅追加，倒序（新在上）。

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
