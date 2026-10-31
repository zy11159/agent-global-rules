# agent-global-rules

把散落在各处的 agent 规则、记忆、可复用文件，收口成一套**单一写入口**的规则体系：

- **引导层**：`<harnessHome>/AGENTS.md` —— 不含规则，只指向规则库并声明写入禁令
- **规则库**：`rule/` —— 跨 agent 通用规则 + 按工作领域拆分的分类规则
- **共用库**：`lib/` —— 可跨项目复用的资产，配总索引与分类/子类索引，按索引读取、禁止遍历

目标是三件事：规则只有一处可写、分类按需读取、资产按索引取用。

## 目录

```
rule/
  _universal.md            # 跨 agent 通用规则 + 分类登记表 + 新建分类流程
  _template.md             # 新建分类的模板
  categories/<名称>.md      # 分类规则，一个工作领域一个文件
lib/
  index.md                 # 共用库总登记表
  <分类>/_index.md          # 分类索引
  <分类>/<子类>/_index.md   # 子类索引，资产文件同目录存放
```

## 安装

把 `skill/` 目录放到任意已扫描的 skill 根目录：

| 生效范围 | 目录 |
|---|---|
| 当前用户（推荐） | `~/.dsh/skills/agent-global-rules/` |
| 仅当前项目 | `<项目>/.dsh/skills/agent-global-rules/` |
| agent 通用约定 | `<项目>/.agents/skills/agent-global-rules/` |

放好后在会话里引用 `agent-global-rules` 即可。skill 从磁盘实时读取，改完不用重启。

## 使用

对 agent 说：**"按 agent-global-rules 建立跨 agent 规则体系"**，并给出规则库路径与现有规则条文。
agent 会按 `skill/SKILL.md` 的六个阶段执行：探测 → 规则库 → 引导层 → 共用库 → 交接其他 agent → 自检。

## 交付其他 agent

`skill/references/agent-handoff.md` 是发给其他 agent 的一次性迁移提示词。它只让对方把自己规则/记忆文件改成指向规则库的指针，**不复制、不重写规则**。直接整段转发即可。

## 设计取舍

- **单写入口优于就近存放**：规则写两处必然漂移，所以引导文件被限制成纯指针。
- **索引优于遍历**：共用库被设计成"索引优先"，否则一次取资产就把上下文占满。
- **英文目录名**：跨 agent、跨 shell 环境下中文路径存在编码风险。
- **资产与规则物理隔离**：`lib/` 里的东西是参考材料，不是已批准的规则。

## 许可

MIT
