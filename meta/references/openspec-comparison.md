# OpenSpec 设计调研 × harness-over-harness 对比与改进

> 调研日期：2026-06-24 ｜ 一手来源：Fission-AI/OpenSpec(GitHub ~34.9k★)、DeepWiki、openspec.pro、腾讯云开发者社区多篇深度解析。
> 目的：判断 OpenSpec 是否与本仓库属同类设计,并提炼可吸取的经验来提升本仓库。

---

## 1. OpenSpec 是什么(一句话)

**面向 AI coding agent 的轻量级「规格驱动开发(SDD, Spec-Driven Development)」框架**:在写第一行代码之前,先让人和 AI 在仓库里就"要构建什么"达成一致,把需求/设计/任务变成结构化、版本化的仓库资产,而非散落在聊天记录里。核心口号:**"Change-as-Code(变更即代码)"**。

---

## 2. OpenSpec 的关键设计(我们要重点看的)

### 2.1 三态分离的目录结构(最值得借鉴)
```
openspec/
├── config.yaml      # 项目级配置(context 全局约束 + rules 工件细粒度规则)
├── schemas/         # 自定义工作流 schema(可替换内置流程)
├── specs/           # 【当前规范 = 真理之源】按能力/领域组织,每个含 spec.md(What/Why) + design.md(How)
└── changes/         # 【活跃变更提案】每个变更一个文件夹
    └── archive/     # 【已完成变更】保留演进历史,日期前缀 YYYY-MM-DD-<name>
```
洞察:把 **「系统现状(specs)」「正在施工(changes)」「历史档案(archive)」三者物理隔离**。Agent 任何时候都能一眼分清"什么是事实、什么在变、什么已成历史"。

### 2.2 单个变更的内部结构
`changes/<change-name>/` 内含:
| 文件 | 作用 |
|------|------|
| `.openspec.yaml` | 元数据(schema、创建日期) |
| `proposal.md` | Why + What(变更意图) |
| `tasks.md` | 实现检查清单 |
| `design.md` | 技术决策(可选) |
| `specs/` | **Delta 文件**——只写"变了什么",不重写整份规范 |

### 2.3 Delta Spec 机制(核心创新)
改需求不重写整份规范,只描述增量,四种语义:
- `ADDED` 新增 → 归档时追加
- `MODIFIED` 修改 → 替换原需求块
- `REMOVED` 弃用 → 删除
- `RENAMED` 重命名 → 保留内容改名

归档时由 **Specs Apply 引擎** 按固定顺序合并:`RENAMED → REMOVED → MODIFIED → ADDED`(顺序有严格因果理由)。

### 2.4 三层验证(机械化质量门)
**格式 → 语义 → 业务逻辑**三层校验,由 CLI 引擎强制:
- 每个 `ADDED/MODIFIED` 需求必须含 `SHALL`/`MUST` 且至少一个场景;
- 量化阈值:Purpose ≥ 50 字符、需求 ≤ 500 字符、Why 段 50–1000 字符、单变更 ≤ 10 个 delta。
- 规范用 **GIVEN-WHEN-THEN** 描述场景,强制"聚焦 What 而非 How"。

### 2.5 双工作流档位(Core / Extended)
| | Core(快速路径) | Extended(细粒度) |
|---|---|---|
| 命令 | `propose → apply → archive`(3 步) | `explore/new/continue/ff/apply/verify/sync/archive` |
| 适用 | 小功能、个人、熟手 | 大功能、团队、需逐阶段审查、新手 |

### 2.6 跨工具命令注入(注册表 + 适配器)
内置 22+ 适配器(Claude Code / Cursor / Windsurf / Copilot / Codex …),把 OpenSpec 的斜杠命令按各工具的路径规则注入其上下文。指令用**构建器模式**分层组装:基础信息 → 项目上下文 → 工件规则 → 模板。

---

## 3. OpenSpec vs harness-over-harness:同类吗?

**同赛道,不同层。** 两者都为 AI agent 提供结构化工作框架,但:

| 维度 | OpenSpec | harness-over-harness |
|------|----------|----------------------|
| 定位 | **执行框架**:在一个项目内驱动 SDD 闭环 | **元框架/生成器**:教 agent 为任意项目搭 harness |
| 关注点 | 需求→规范→实现→归档的可预测性 | 总指挥/记忆/编排/沉淀的完整 harness |
| 产物 | specs / changes / delta / tasks | AGENTS.md / TODO / LOG / ideas / skills … |
| 记忆 | specs(现状) + archive(历史) | TODO(机器) + LOG(人类) 双轨 |
| 质量门 | CLI 三层验证(强机械化) | 文档化 checklist + 闭环自检(偏人工) |
| 范围 | 单项目 | 跨项目通用 |

结论:**不冲突,可互补**。OpenSpec 可作为我们"目标项目 harness"里**关于"如何管理需求变更"的一种可选 SOP / skill**;而它最成熟的几个机制,正好补上我们当前偏"软约束"的短板。

---

## 4. 可吸取的 5 条经验(→ 落地到本仓库)

> 优先级:🔴 强烈建议 / 🟡 建议 / 🟢 可选

### 经验 1 🔴 引入"三态分离"思想,强化记忆结构
**学 OpenSpec 的 specs/changes/archive 物理隔离。** 我们目前 `TODO.md` 把"当前/待办/已完成"放一个文件,项目一大就臃肿。
- **改进**:在模板里明确"现状 vs 进行中 vs 归档"的分区;大型目标项目建议把已完成事项定期滚动到 `LOG.md`/归档区,保持 `TODO.md` 轻量。
- 与红线 R2(只追加不删改)兼容:是"移动归档"而非"删除"。

### 经验 2 🔴 用 Delta(增量)思维管理"变更",而非每次重写
**学 Delta Spec 的 ADDED/MODIFIED/REMOVED/RENAMED。** 我们的 `AGENTS.md` 是"文档免疫系统",但没规定"怎么改"。
- **改进**:为目标项目的 `AGENTS.md`/规范变更约定**增量记录**——每条新规则标注它新增/修改/废弃了什么 + 对应的真实教训,而不是悄悄重写历史。可在 `LOG.md` 模板加一个"规则变更(delta)"栏位。

### 经验 3 🔴 把"软 checklist"升级为"机械化质量门"
**学三层验证 + 量化阈值。** 我们目前阶段 3 的"换位自检"靠 agent 自觉,易被 first-plausible-solution bias 跳过。
- **改进**:沉淀一个 `verify-harness` 的 **slash command 或 skill**,机械化检查目标项目是否齐备 `AGENTS.md/TODO.md/LOG.md`、命令是否可跑、红线是否覆盖真实风险。这正是我们 `ideas.md` 里已记的"harness 自检脚本",OpenSpec 验证了其价值,**应提优先级**。

### 经验 4 🟡 提供双档位工作流(轻/重)
**学 Core / Extended Profile。** 我们的 6 阶段 SOP 对小项目偏重。
- **改进**:在 `GUIDE/harness-construction.md` 顶部增加**「MVP 快速路径(AGENTS+TODO+LOG 三件套) vs 完整路径(6 阶段)」**的分流指引,让 agent 按项目规模选档位。(已有 MVP 速查,可显式提为"档位选择"。)

### 经验 5 🟡 规范"可验证的写法"(GIVEN-WHEN-THEN / SHALL)
**学强制场景化、聚焦 What。** 让规则/需求**可被验证**而非空泛。
- **改进**:在 `templates/AGENTS.template.md` 与红线写法中提倡"每条规则可观测/可验证";为有需求管理诉求的目标项目,在 `references` 推荐 GIVEN-WHEN-THEN 写法。

### (不采纳)跨工具适配器注册表
OpenSpec 的 22+ 工具适配器是其作为"产品"的工程投入,我们是方法论仓库,**不需要**自建适配器层——直接复用 `AGENTS.md` 这一跨工具事实标准即可。记录在此以备未来需要。

---

## 5. 一句话总结

OpenSpec 把"**需求变更的可预测性**"做到了机械化、可验证的极致;harness-over-harness 把"**整套 harness 的可复制性**"做成了元框架。
我们应吸收它的 **三态分离、Delta 增量、机械化验证门、双档位** 四大经验,把当前偏"软约束"的 harness 升级为"软硬结合";同时可把 OpenSpec 本身作为目标项目的一个**可选需求管理 skill** 来推荐。

---

## 来源(抓取于 2026-06-24)
- GitHub:https://github.com/Fission-AI/OpenSpec
- 官网:https://openspec.pro/
- 目录结构(DeepWiki):https://deepwiki.com/Fission-AI/OpenSpec/4.1-directory-structure
- 工作流解析:https://cloud.tencent.com/developer/article/2656207
- 最佳实践/Delta/三层验证:https://cloud.tencent.com/developer/article/2649108
