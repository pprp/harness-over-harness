<!--
模板：Slash Command（简单、线性、靠提示词即可描述的可复用流程）。
用法：在目标项目 commands/<command-name>.md 创建。
判断是否该用 Command 而非 Skill：见 harness-over-harness/meta/GUIDE/orchestration.md 第 3 节。
-->
---
name: {{command-name}}
description: {{一句话：这个命令做什么}}
---

# /{{command-name}}

## 用途
{{这个命令把哪个简单重复流程一键化}}

## 参数（可选）
- `$ARGUMENTS` / `{{参数名}}`：{{说明}}

## 执行步骤（提示词）
1. {{步骤一}}
2. {{步骤二}}
3. {{完成后：更新 TODO.md / LOG.md（若该流程值得记账）}}

## 约束
- 遵守 `AGENTS.md` 红线。
- {{该命令特有的注意事项}}
