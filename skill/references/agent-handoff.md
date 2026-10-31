# 交接：把另一个 agent 的规则与记忆改为指向规则库

**这是一次性迁移任务，不是规则集。** 不要把它当规则长期遵循，不要复制进任何规则目录。完成后回报即可。

## 目标
让**你所在的 agent** 的持久化规则与记忆，指向共享规则库。**不要重写、复述或重新生成规则**——规则库已存在。你只负责改指向。

## 硬性约束
- 规则库（对你只读）：`D:\_AI\ALL_agent\rule`
  - `_universal.md` —— 跨 agent 通用规则 + 分类登记表
  - `categories\<名称>.md` —— 分领域规则
- **禁止**在 `D:\_AI\ALL_agent\rule` 下创建、复制、修改、改写任何内容。
- **禁止**把规则正文写进你自己的规则/记忆文件。这些文件的最终内容只能是指针。
- 已经指向规则库的，说明已完成——只验证，不重做。

## 步骤
1. **发现**。找出你的 agent 实际加载的全部规则/指令文件与记忆库。**搜索，不要假设**：扫你的 home 配置目录、项目根目录，以及 agent 自身文档声明的记忆位置。记录每个找到的路径。
2. **备份**。每个要替换的文件先复制为同目录下 `<文件名>.bak-YYYYMMDD-HHMMSS`。
3. **替换**。把这些文件的规则正文替换为下面这段指针（措辞可按你的工具微调，实质内容不变）：
   ```
   # 规则不在此处 —— 占位规则无效
   所有 agent 规则与跨 agent 全局规则存放于 D:\_AI\ALL_agent\rule
   读取顺序：先 _universal.md，之后只读 categories\ 下匹配的分类文件。
   禁止把规则写入本文件或其他任何默认规则/记忆位置。
   任何规则写入或修改都必须落在 D:\_AI\ALL_agent\rule。
   ```
4. **清空但不删除**。移除规则/记忆的**内容**，保留文件及其加载机制——删文件可能破坏指令加载。内容清空，留下指向规则库的存根。
5. **数据库型记忆**（sqlite / jsonl / 键值存储）：先试工具自带的删除/清空命令。没有该命令时**不要**手改或删除文件——带上路径报告并停下。
6. **验证**。重读每个改动过的文件，确认：(a) 含指针；(b) 无规则正文残留；(c) 路径精确为 `D:\_AI\ALL_agent\rule`。
7. **回报**：改了哪些路径、备份了哪些、哪些跳过及原因、哪些存储无法修改。

## 常见位置（须先验证，非完整清单）
| Agent / 工具 | 规则文件 | 记忆存储 |
|---|---|---|
| DSH | `C:\Users\12574\.dsh\AGENTS.md` | 无 |
| Claude Code | `C:\Users\12574\.claude\CLAUDE.md`、`<项目>\CLAUDE.md` | 工具自管理 |
| Codex | `C:\Users\12574\.codex\AGENTS.md`、`config.toml` | `C:\Users\12574\.codex\memories_1.sqlite` |
| Cursor | `<项目>\.cursor\rules\*`、`.cursorrules` | 工具自管理 |
| GitHub Copilot | `<项目>\.github\copilot-instructions.md` | — |
| Gemini CLI | `C:\Users\12574\.gemini\GEMINI.md` | — |
| Trae | `C:\Users\12574\.trae-cn\`（settings / skill-config） | `C:\Users\12574\.trae-cn\memory` |
| WorkBuddy | `C:\Users\12574\.workbuddy\BOOTSTRAP.md`、`SOUL.md`、`IDENTITY.md` | `C:\Users\12574\.workbuddy\MEMORY.md`、`memory\`、`workbuddy.db` |

未知 agent → 以第 1 步的实际发现为准。**未经确认存在的路径，不得凭猜测写入。**
