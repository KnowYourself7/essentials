# Skill 开发信息源

版本：v1.0，2026-10-17
用途：每次改 skill、子代理、插件的任何文件，动手前和交付前到这里挑来源核对（铁律第 15 条）。只列 Anthropic 官方和它发布的开放标准；社区文章可以参考，不作依据。
网址逐一在 2026-10-17 打开核对过。

## 来源表

| 编号 | 来源 | 网址 | 管什么 | 什么时候查 |
|---|---|---|---|---|
| S1 | Agent Skills 规范 | https://agentskills.io/specification | 格式的底线：目录结构；frontmatter 字段（name、description 必填，license、compatibility、metadata、allowed-tools 可选）和各自长度、命名限制；scripts、references、assets 约定；渐进加载三层（元数据约 100 token、正文建议 5000 token 内、附属文件按需）；SKILL.md 不超过 500 行；文件引用只走一层；skills-ref 校验器 | 新建 skill；改 frontmatter；加或改 references、scripts、assets |
| S2 | Agent Skills 官网 | https://agentskills.io （索引 https://agentskills.io/llms.txt） | 规范变更、支持的产品 | 规范页找不到某条时 |
| S3 | Claude Code 文档：Skills | https://code.claude.com/docs/en/skills | Claude Code 里的行为：skill 放哪决定哪些会话加载；frontmatter 全表（含 Claude Code 特有的 user-invokable、argument-hint、disable-model-invocation、context: fork 等）；`!` 反引号动态注入；$ARGUMENTS；谁能触发；skillOverrides；skill-creator 评测；分发方式 | 改任何 Claude Code 特有字段、触发方式、参数、是否在子代理里跑 |
| S4 | Claude Code 文档：Plugins | https://code.claude.com/docs/en/plugins （子页：/plugins/create、/plugins/manifest-reference、/plugins/components、/plugin-evals） | plugin.json 和 marketplace.json 字段；插件能装哪些组件；启用后对每个会话的上下文开销；评测 | 改 .claude-plugin/ 下任何文件；改 agents、hooks 组件；发布 |
| S5 | Claude Code 文档：Subagents | https://code.claude.com/docs/en/sub-agents | agents/*.md 的 frontmatter（tools、model、权限）、怎么派、fork 当前对话 | 改 agents/ 下任何文件 |
| S6 | Claude 平台文档：Skills overview | https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview | 架构和三层加载的原理；name 不能含保留字 anthropic、claude；各平台运行环境限制（API 无网络、Claude Code 有网络但别装全局包）；安全注意 | 新建 skill；要解释“为什么这样设计”时 |
| S7 | Claude 平台文档：Skill authoring best practices | https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices | 写法的全部规则：只写模型不知道的；自由度匹配脆弱程度；description 第三人称、写清做什么和何时用；命名用动名词；引用一层深、长参考文件加目录；工作流写成清单加校验回路；不写时效信息；术语一致；脚本“解决不推诿”、不留魔数、写清是执行还是阅读；MCP 工具写全名；先建评测再写文档；文末交付检查清单 | 改 SKILL.md 正文、description、scripts；交付前过清单 |
| S8 | Anthropic 工程文章：Equipping agents for the real world with Agent Skills | https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills | 设计理由：skill 和上下文窗口、skill 和代码执行、评测与迭代、安全 | 设计新功能、取舍拿不准时 |
| S9 | anthropics/skills 仓库 | https://github.com/anthropics/skills | 官方示例 skill（docx、pdf、pptx、xlsx 等）、template/ 起手模板、spec/ 规范副本 | 想看“官方自己怎么写”时 |
| S10 | agentskills/agentskills 仓库 | https://github.com/agentskills/agentskills | 规范源码、skills-ref 校验器（`skills-ref validate ./my-skill`） | 跑格式校验 |

## 改动类型对应查哪里

| 改了什么 | 查 | 看哪一节 |
|---|---|---|
| frontmatter 任何字段 | S1、S3 | S1 的 SKILL.md format；S3 的 Frontmatter reference |
| name、description | S7、S6 | S7 的 Naming conventions、Writing effective descriptions；S6 的 Skill structure（保留字） |
| SKILL.md 正文（流程、规则、措辞） | S7 | Core principles、Workflows and feedback loops、Content guidelines、Common patterns、Anti-patterns |
| 新增或拆分 references/ 文件 | S1、S7 | S1 的 Progressive disclosure、File references；S7 的 Progressive disclosure patterns、Avoid deeply nested references、Structure longer reference files |
| 新增或改 scripts/ | S7 | Advanced: Skills with executable code（Solve don't defer、Provide utility scripts、Create verifiable intermediate outputs、Package dependencies） |
| 触发方式、参数、在子代理里跑 | S3 | Configure skills、Advanced patterns |
| agents/*.md | S5 | Configure subagents |
| .claude-plugin/plugin.json、marketplace.json | S4 | manifest-reference、components |
| 交付前 | S7 | Checklist for effective Skills |

## 核对流程（每次改 skill 都走）

1. 先定改动类型，按上表挑 1 到 2 个来源，不多挑。
2. 用 WebFetch 读那一节的原文，不凭记忆；读不到用 Exa；不用 Firecrawl。
3. 把设计逐条对上，分三类：符合；不符（改）；规范没写（标“本项目设计”）。
4. 回答里只写一行对照结果，细节进项目的改动日志或功能规格（`collaboration.md` 四块回答的“对照依据自检”）。
5. 能跑的校验都跑：skills-ref validate（装了的话）、项目自己的测试、`claude plugin validate .`。

## 已知的坑（核对时顺手看）

- `allowed-tools` 在开放规范里是实验字段，Claude Code 的 skills 文档不把它当规范字段；本项目约定不用。
- Claude 平台文档规定 name 不能含 anthropic、claude；Claude Code 新版也把 `claude-` 开头的插件名当保留名。
- Claude Code 的 frontmatter 字段比开放规范多，字段只在 Claude Code 生效的，S3 表里有标注；写给别的平台用的 skill 不要依赖它们。
- 官方文档更新频繁，核对以当天打开的原文为准，不以本文件的摘要为准。

## 维护

- 每次发现网址失效，在表里标“已失效，替代：…”，不直接删。
- Claude Code 大版本更新后，把 S3、S4、S5 重新过一遍。
- 新增来源只收 Anthropic 官方、agentskills.io 和它们的 GitHub 仓库；社区文章放进 `skill-dev-rules.md` 的出处，不进本表。

## 修订记录

- v1.0 2026-10-17：首版，10 个来源，附改动类型对应表和核对流程。起因：Kai 要求以后每次改 skill 都到固定信息源核对最佳实践。
