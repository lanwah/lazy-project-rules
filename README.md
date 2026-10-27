# lazy-project-rules

面向 Cursor Agent 的**用户级**项目规则集：少写重复代码、少踩假值陷阱——「懒开发」优先。

规则正文为 Cursor Rules（`.mdc`），可放在本机 `%USERPROFILE%\.cursor\rules\`（Windows）或 `~/.cursor/rules/`（macOS / Linux），对所有工作区生效；也可配合加载器只在指定仓库启用。

## 仓库名说明

参考名 `lazy-project-rule` 规范为：

| 项 | 取值 |
|----|------|
| 仓库名 | `lazy-project-rules` |
| 风格 | 全小写 kebab-case |
| 复数 | 本仓是多条规则的集合，用 `rules` |

## 规则一览

| 文件 | 作用域 | 摘要 |
|------|--------|------|
| [`coding-principles.mdc`](coding-principles.mdc) | `alwaysApply` | DRY（禁止复制改名/平行实现）、开闭原则、组合优于继承 |
| [`web-nullish-vs-zero.mdc`](web-nullish-vs-zero.mdc) | `**/*.{ts,tsx,js,jsx,vue,svelte,html}` | 区分「无值」与合法有值（`0` / `""` / 空白 / `false`），禁止 `\|\|` / `!value` 抹平语义 |
| [`codegraph-project-path.mdc`](codegraph-project-path.mdc) | `alwaysApply` | CodeGraph `codegraph_explore` 必须传 `projectPath`，多根工作区禁止依赖 default 索引 |
| [`agent-coordinator-runtime.mdc`](agent-coordinator-runtime.mdc) | `alwaysApply` | 主 Agent 协调：非平凡任务委派子 Agent；Brief / 门控 / C4 验收门禁 |

协调流程细则与用户 Skill：`%USERPROFILE%\.agents\skills\agent-coordinator\`；验收子 Agent：`%USERPROFILE%\.cursor\agents\coordinator-verifier.md`。

本仓自身还启用了序号提交规范（见 [`.cursor/rules/sequential-commit-msg.mdc`](.cursor/rules/sequential-commit-msg.mdc)）：提交首行形如 `#N 说明`。

## 安装与使用

### 方式 A：用户级全局规则（推荐）

将需要的 `.mdc` 放到 Cursor 用户规则目录：

```text
Windows:  %USERPROFILE%\.cursor\rules\
macOS / Linux:  ~/.cursor/rules/
```

例如本仓库本身就可以是该目录的 git 工作区：改规则 → 提交 → 推送，多机保持同步。

重启 Cursor 或新开 Agent 会话后，带 `alwaysApply: true` 的规则会对所有项目生效；带 `globs` 的规则在匹配文件上下文中生效。

### 方式 B：只在指定项目启用（不复制正文）

若希望规则正文仍只维护在用户目录一份，而项目里只挂「去读用户规则」的薄加载器，可使用本机 Skill `load-user-cursor-rules`（常见路径：`%USERPROFILE%\.agents\skills\load-user-cursor-rules\`）。

在目标仓库对话中说明「按 load-user-cursor-rules 为本项目启用用户规则」即可；**不要**把本仓 `.mdc` 复制进各个业务仓库。

### 方式 C：拷进单个项目

将选定的 `.mdc` 复制到项目的 `.cursor/rules/`。适合只想采用部分规则、且接受与用户级目录分叉的场景。

## 规则文件格式

```markdown
---
description: 简短说明（规则选择器中展示）
globs: "**/*.{ts,tsx}"   # 可选；文件匹配时应用
alwaysApply: false         # true 则每轮会话都应用
---

# 标题

正文…
```

详见 [Cursor 文档：Rules](https://cursor.com/docs/context/rules)。

## 设计原则（给贡献者）

1. **一条规则一件事**：总则与领域细则分开，避免巨型杂烩文件。
2. **可执行、可对照**：尽量给 ❌ / ✅ 短例，少写空话。
3. **与 DRY 一致**：空值判断、校验等应指向「项目内统一工具」，不要鼓励各处复制粘贴。
4. **用户级正文唯一来源**：跨项目复用时优先用户目录 + 加载器，避免把同一套规则 fork 进多个业务仓。

## 许可

未另声明时，可按个人 / 团队内部约定使用与修改。若你 fork 后公开发布，建议自行补充 `LICENSE`。
