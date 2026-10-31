---
name: agent-global-rules
description: 从零落地一套跨 agent 全局规则体系——引导层 AGENTS.md、规则库 rule/（通用规则+分类规则）、共用库 lib/（可复用资产+索引）。当用户要求建立/重建 agent 全局规则、跨 agent 规则、共用库、或把规则与资产集中到统一目录时使用。
whenToUse: 用户要建立跨 agent/多 agent 共享的规则库与资产库；或要求把散落的 agent 规则、记忆、可复用文件统一收口到一个目录并声明读写禁令。
---

# 跨 agent 全局规则体系 · 落地蓝图

本 skill 只给**结构与流程**，不含具体规则条文。规则内容以用户提供的为准；
空白处用 `templates/` 里的模板补，模板只是起点，不是标准答案。

## 核心概念

| 层 | 位置 | 职责 | 谁能写 |
|---|---|---|---|
| 引导层 | `<harnessHome>/AGENTS.md` | 指向规则库 + 写入禁令 + 读取顺序 | 建立时写一次，之后不写规则 |
| 规则库 | `<RULE_DIR>` | 通用规则 `_universal.md` + 分类规则 `categories/<名称>.md` | agent 改规则只能改这里 |
| 共用库 | `<LIB_DIR>` | 可跨项目复用的资产 + 总索引 + 分类/子类索引 | agent 经用户触发后写入 |

三条不变量，落地时必须保住：

1. **单一写入口**：规则只存在于 `<RULE_DIR>`。任何其他位置（引导文件、项目 `AGENTS.md`、`CLAUDE.md`、记忆库、聊天记录）都不承载规则。
2. **分类分层读取**：先读通用规则 → 判断工作分类 → 只读匹配的分类。不相干分类不读。
3. **索引优先**：读共用库前先读索引，命中才读文件。禁止遍历共用库目录。

## 变量

先确定再动手，全部用绝对路径：

- `RULE_DIR` — 规则库根，默认 `D:\_AI\ALL_agent\rule`（用户指定优先）
- `LIB_DIR` — 共用库根，默认与 `RULE_DIR` 同级的 `lib`（即 `D:\_AI\ALL_agent\lib`）
- `HOME_AGENTS` — 引导文件，默认 `<harnessHome>/AGENTS.md`。DSH 的 harness home 默认 `~/.dsh`，可用 `$DSH_HOME` 覆盖；其他 agent 见本文件末尾对照表
- `PROJECT_ROOT` — 当前项目根（有 `.git` 的最近祖先目录）

## 步骤

分阶段，每阶段结束**先验证再进下一阶段**。任何阶段已满足 → 跳过，不重做（幂等）。

### 阶段 0 · 探测

1. 确认 `RULE_DIR`、`LIB_DIR`、`HOME_AGENTS` 的绝对路径；确认 `HOME_AGENTS` 所在 agent 确实会加载它（查该 agent 的文档或配置，不靠猜）。
2. 盘点已存在的规则/记忆文件，列成清单，报告给用户后再动。
3. 若用户未指定路径，用默认值，并在动手前一句话确认。

### 阶段 1 · 规则库

1. 建 `<RULE_DIR>` 与 `<RULE_DIR>/categories/`。
2. 写 `_universal.md`：通用规则（用 `templates/_universal.template.md` 起步，替换为用户给出的原始规则）+ 分类登记表 + 新建分类流程。
3. 写 `_template.md`：新建分类的模板。
4. 用户已给出某分类规则 → 按 `templates/category.template.md` 落成 `categories/<名称>.md`，并在登记表加一行。
5. 验证：登记表与 `categories/` 下实际文件一一对应，无遗漏、无孤立条目。

### 阶段 2 · 引导层

1. 备份已有 `HOME_AGENTS`（同目录 `<文件名>.bak-YYYYMMDD-HHMMSS`）。
2. 按 `templates/bootstrap-AGENTS.template.md` 写入**只含指针与禁令**的内容：规则库路径、禁止在其他位置写规则、读取顺序（通用规则 → 分类 → 新建分类流程）。把模板里的 `<RULE_DIR>`、`<LIB_DIR>`、`<harnessHome>` 替换为阶段 0 确认的绝对路径。
3. **不得**把规则正文放进引导文件。若已有正文，正文迁移进 `_universal.md` 后再清空。
4. 验证：文件内无任何规则条文；指向路径真实存在。

### 阶段 3 · 共用库

1. 建 `<LIB_DIR>`、`<LIB_DIR>/index.md`（总登记表：用途、使用约定、登记规则、分类索引、维护规则）。
2. 分类索引与子类索引：`<LIB_DIR>/<分类>/_index.md`、`<LIB_DIR>/<分类>/<子类>/_index.md`，用 `templates/lib-index.template.md`。
3. 分类名与 `<RULE_DIR>/categories/` 的规则分类保持一致，一个分类一个目录。
4. 索引未收资产时**不要**预建空分类目录；按需创建。
5. 验证：任取一条资产路径假设，检查 `index.md → 分类 _index.md → 子类 _index.md → 文件` 四跳都能命中。

### 阶段 4 · 交接其他 agent

1. 把 `references/agent-handoff.md` 交付给用户，由其发给其他 agent 执行。
2. 交接内容只做三件事：找到自己的规则/记忆文件、备份、改写成指向 `RULE_DIR` 的指针。**禁止**让它们复制或重写规则。
3. 验证：被交接 agent 回报的路径清单中，无一份文件含规则正文。

### 阶段 5 · 自检

- [ ] 引导文件不含规则正文，只指向 `RULE_DIR`
- [ ] `_universal.md` 含通用规则 + 登记表 + 新建分类流程
- [ ] 登记表与 `categories/` 实际文件一致
- [ ] `LIB_DIR` 索引四跳可达
- [ ] 共用库读取规则写明"索引优先、禁止遍历"
- [ ] 所有写入可复现（路径为绝对路径，无本机用户名硬编码以外的意外差异）

## 常见错误

- **把规则写进引导文件**，或让别的 agent 复制一份规则过去 → 出现多份副本，改一处不生效。必须单写入口。
- **跳过索引直接读共用库** → 上下文被资产全文占满。索引优先是硬性要求。
- **用中文目录名** → 部分 agent、命令行、脚本在非 UTF-8 环境下解析失败。目录名用英文小写短横线。
- **共用库与规则库混放** → `lib/` 里的资产会被误当规则。两者必须是 `RULE_DIR` 与 `LIB_DIR` 两个独立根。
- **建立时写成 BOM 的 UTF-8** → 部分解析器把 BOM 当正文首字符，frontmatter 失效。写 UTF-8 无 BOM。
- **在规则库里造空分类** → 登记表出现大量空条目，判断分类时噪音大。按需创建。

## 文件

本 skill 自带的参考文件：

- `templates/bootstrap-AGENTS.template.md` — 引导层文件模板（指针 + 禁令 + 读取顺序）
- `templates/_universal.template.md` — 通用规则文件模板（含共用库章节与登记表）
- `templates/category.template.md` — 新建分类的空白模板
- `templates/categories/frontend.md` — 一个已填写的分类样例
- `templates/lib/index.template.md` — 共用库总登记表模板
- `templates/lib/category-index.template.md` — 分类/子类索引模板
- `references/agent-handoff.md` — 发给其他 agent 的一次性迁移提示词

模板是起点，不是标准答案。用户提供规则条文时，以用户条文为准，模板只提供结构。

## 常见 agent 的引导文件位置

需先验证存在再使用，不同版本可能变化：

| Agent / 工具 | 引导文件 | 记忆存储 |
|---|---|---|
| DSH | `~/.dsh/AGENTS.md`（可用 `$DSH_HOME` 覆盖） | 无 |
| Claude Code | `~/.claude/CLAUDE.md`、`<项目>/CLAUDE.md` | 工具自管理 |
| Codex | `~/.codex/AGENTS.md` | `~/.codex/memories_1.sqlite` |
| Cursor | `<项目>/.cursor/rules/*`、`<项目>/.cursorrules` | 工具自管理 |
| GitHub Copilot | `<项目>/.github/copilot-instructions.md` | — |
| Gemini CLI | `~/.gemini/GEMINI.md` | — |
| Trae | `~/.trae-cn/`（settings / skill-config） | `~/.trae-cn/memory` |
| WorkBuddy | `~/.workbuddy/BOOTSTRAP.md`、`SOUL.md`、`IDENTITY.md` | `~/.workbuddy/MEMORY.md`、`memory/` |

没有专用规则文件机制的 agent → 以阶段 0 的实际发现为准，不硬套。

## 报告要求

每阶段完成后向用户报告：实际使用的绝对路径、新建/修改的文件清单、跳过的阶段及原因、尚存的未验证假设。
不要用"已完成"概括，要给出可核对的路径。
