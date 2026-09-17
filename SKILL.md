---
name: create-project-skill
description: 在当前项目中创建或迁移项目级 skill。项目 skill 一律放在项目根目录的 `.agents/skills/<skill-name>/SKILL.md` 且以中文撰写，通过 `skill-creator` skill 完成创建，并在项目 `AGENTS.md` 中登记调用场景与索引。当用户要求"给这个项目写个 skill"、"创建项目 skill"、"把项目 skill 迁移到 .agents/skills"，或发现项目里存在旧版 `docs/skills/` 目录、`SKILL.zh.md` 等翻译副本时使用。
---

# create-project-skill

在当前项目中创建、登记或迁移项目级 skill。

## 路径约定

- **MUST** 将项目 skill 放在 `<项目根目录>/.agents/skills/<skill-name>/SKILL.md`。
- **MUST NOT** 使用旧版 `docs/skills/` 目录；**IF** 发现项目内存在 `docs/skills/` -> **MUST** 主动提示用户并执行迁移（见下方"旧版迁移"）。
- **WHEN** 撰写或迁移项目内 skill（含 frontmatter 的 `name`/`description` 与正文）-> **MUST** 直接以中文撰写 `SKILL.md`（技术名词、命令、路径可保留英文原文），**MUST NOT** 额外维护 `SKILL.zh.md` 等翻译副本；**IF** 发现项目内已存在 `SKILL.zh.md` 或英文旧版 `SKILL.md` 与翻译副本并存 -> **MUST** 执行迁移（见下方“翻译副本迁移”）。
- **WHEN** 在 SKILL.md 中引用项目内路径 -> **MUST** 使用项目内相对路径，**MUST NOT** 使用绝对路径。

## 创建流程

1. **加载 skill-creator**：读取 `skill-creator` skill，按其规范执行创建（目录结构、frontmatter 的 `name`/`description`、正文组织方式均遵循 skill-creator 的要求）。
2. **确定落点**：在项目根目录下创建 `.agents/skills/<skill-name>/SKILL.md`；skill 引用的脚本、模板等附属文件放在同一目录内。
3. **登记到项目 AGENTS.md**：创建完成后，**MUST** 在项目的 `AGENTS.md` 中补充该 skill 的条目，包括：
   - skill 名称与路径（`.agents/skills/<skill-name>/`）；
   - 调用场景：什么时候应该触发这个 skill（用结构化控制词写法，如 `**WHEN** ... -> **MUST** 使用 \`<skill-name>\` skill`）；
   - **IF** 项目不存在 `AGENTS.md` -> **MUST** 新建。
4. **验证**：确认 skill 目录结构完整、frontmatter 合法、AGENTS.md 索引与实际路径一致。

## 英文旧版迁移（英文 SKILL.md -> 中文 SKILL.md）

**WHEN** 在项目 skill 目录中发现 `description` 或正文为英文的 `SKILL.md`（无论是否存在翻译副本、是否正在执行其他迁移）-> **MUST** 按以下步骤就地翻译：

1. 将 `description` 与正文完整译为中文，**MUST** 保留 frontmatter 的 `name` 标识与全部技术内容（命令、路径、参数、配置快照值），**MUST NOT** 借翻译之机增删规则。
2. 语言转换是机械操作，**MUST NOT** 以「内容可能过期」「超出当前任务范围」「留待下次维护」为由保留英文版或推迟；翻译与内容时效验证是两件事，不得捆绑延迟。
3. **IF** 翻译中发现具体内容疑点（路径不存在、命令失效、快照过期）-> **SHOULD** 当场验证后修正，无法验证的 **MUST** 在该条旁标注待核实并向用户报告，**MUST NOT** 因此搁置整个翻译。
4. 向用户汇报翻译的 skill 清单及发现并处理的疑点。

## 翻译副本迁移（SKILL.zh.md -> 中文 SKILL.md）

**WHEN** 在项目 skill 目录中发现 `SKILL.zh.md`、`SKILL.en.md` 等翻译副本，或英文旧版 `SKILL.md` 与中文副本并存 -> **MUST** 按以下步骤迁移：

1. 对比 `SKILL.md` 与翻译副本内容，**MUST** 对两份内容求并集：任一方独有的规则、步骤或说明都 **MUST** 保留并统一翻译为中文，合并成一份完整的中文版本，**MUST NOT** 只以其中一份为准而丢弃另一方内容。
2. 用合并后的中文内容覆盖 `SKILL.md`，删除翻译副本文件；**IF** 文件被 Git 跟踪 -> **SHOULD** 使用 `git rm`，**MUST NOT** 直接 `rm`。
3. 检查项目 `AGENTS.md`、README 及其他 skill 中对 `SKILL.zh.md` 等副本路径的引用并清除。
4. 向用户汇报合并取舍与删除的文件清单。

## 旧版迁移（docs/skills -> .agents/skills）

**WHEN** 在项目中发现 `docs/skills/` 目录 -> **MUST** 按以下步骤迁移：

1. 列出 `docs/skills/` 下所有 skill，向用户报告清单并确认迁移。
2. 将每个 skill 目录整体移动到 `.agents/skills/` 下，保持目录名与内部结构不变；**IF** 文件被 Git 跟踪 -> **SHOULD** 使用 `git mv`。
3. 修正项目 `AGENTS.md`、README 及其他文档中对 `docs/skills/` 的引用，改为 `.agents/skills/`。
4. 按"创建流程"第 3 步检查每个迁移过来的 skill 是否已在项目 `AGENTS.md` 中登记调用场景，缺失则补齐。
5. 迁移完成后删除空的 `docs/skills/` 目录，并向用户汇报迁移结果。
