# Ideas — harness-over-harness

> 灵感库：值得做但**当前不做**的想法。评估后要做的搬去 `TODO.md`；放弃的标删除线并写原因。

## 候选想法

- **harness 自检脚本/命令（机械化校验）** 🔴 已被 OpenSpec 三层验证佐证价值，提优先级
  - 价值：用脚本/slash command 检查目标项目是否齐备 `AGENTS.md`/`TODO.md`/`LOG.md`、命令是否可跑、红线是否覆盖真实风险，把"闭环自检"从口头变机械化。对抗 first-plausible-solution bias。
  - 代价/风险：需为不同技术栈适配，可能增加维护负担（注意熵治理）。
  - 触发条件：当 SOP 阶段 3 的"换位测试"被反复手动执行 ≥3 次时。
  - 参考：`references/openspec-comparison.md` 经验 3。

- **三态分离的记忆结构（学 OpenSpec specs/changes/archive）** 🔴
  - 价值：把"系统现状 / 进行中 / 已归档"物理隔离，项目变大时 `TODO.md` 不臃肿。
  - 做法：模板里明确分区；已完成事项定期滚动到归档区/`LOG.md`（移动而非删除，兼容红线 R2）。
  - 参考：`references/openspec-comparison.md` 经验 1。

- **Delta（增量）式规则/需求变更记录（学 OpenSpec Delta Spec）** 🔴
  - 价值：规则变更标注 ADDED/MODIFIED/REMOVED/RENAMED + 对应真实教训，不悄悄重写历史。
  - 做法：`LOG.template.md` 增"规则变更(delta)"栏位。
  - 参考：`references/openspec-comparison.md` 经验 2。

- **双档位工作流（学 OpenSpec Core/Extended）** 🟡
  - 价值：小项目走 MVP 快速路径，大项目走完整 6 阶段，避免过度流程。
  - 做法：`GUIDE/harness-construction.md` 顶部加"档位选择"分流。
  - 参考：`references/openspec-comparison.md` 经验 4。

- **可验证写法规范（GIVEN-WHEN-THEN / SHALL）** 🟡
  - 价值：规则/需求可观测、可验证，而非空泛。
  - 做法：`templates/AGENTS.template.md` 提倡每条规则可验证；需求管理重的项目在 references 推荐 G-W-T。
  - 参考：`references/openspec-comparison.md` 经验 5。

- **把"搭 harness"封装成一个 Skill**
  - 价值：让 agent 在用户说"给这个项目搭 harness"时自动加载完整 SOP，无需人工指路。
  - 代价/风险：现以 `GUIDE/` 形式存在已够用；过早封装可能抽象错。
  - 触发条件：实战使用 ≥3 次、流程稳定后。

- **多 agent 编排范例库**
  - 价值：在 `references/` 沉淀真实的 subagent/team 编排案例（含 prompt 与切分方式），降低后续判断成本。
  - 触发条件：积累到 ≥3 个真实编排案例后整理。

- **目标项目类型分册模板**
  - 价值：为常见项目类型（前端 / 后端服务 / ML 训练 / CLI 工具）提供更具体的 `AGENTS.md` 子模板。
  - 触发条件：发现某类项目反复出现且通用模板填空成本高时。

## 已采纳（转入 TODO）
- （无）

## 已放弃
- （无）
