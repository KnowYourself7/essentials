# Skill 开发铁律

版本：v1.2，2026-09-27
适用：所有基于 Claude Code / Agent Skills 的 skill、插件、子代理的开发与二次开发
正本：本文件。各项目的开发计划只引用条目编号，并补充项目自己的具体值（见末尾"项目落地表"），不复制全文。

## 为什么有这份文件

2026-09-25 之前，claude-blog 二次开发一次改了十几个功能，结果出问题时查不出在哪，只能整体回滚。回滚后总结出这些规则，并用 Anthropic 官方规范和工程文章逐条核对。规则共 13 条，分三组，按开发的三个时刻使用：动手之前、写说明书时、验收时。第 13 条是后补的，归在"动手之前"，编号不重排，免得各项目计划里引用的条目号错位。

来源标注：〔官方规范〕= Claude Platform《Skill authoring best practices》；〔Anthropic 文章〕= 《Building effective agents》《Effective context engineering for AI agents》《How we built our multi-agent research system》《Improving skill-creator》；〔superpowers〕= obra/superpowers 的 writing-skills；〔Red Hat〕= 《Building skills for AI agents: pitfalls and best practices》；〔业界文章〕= MindStudio《What Is the Agent Handoff Pattern?》、TianPan《The Output Coupling Trap》；〔实践〕= 自己踩坑总结。

---

## 一、动手之前（每次开工先过第 1 到 3 条和第 13 条）

### 1. 依赖三问，先答再改 〔实践；呼应 Anthropic 文章"派子代理前先写清目标、输出格式、工具、边界"〕

每加一个功能，先写三行：

- 它吃什么文件？（输入）
- 它吐什么文件？（产物）
- 它前面必须先有哪一步？（前置）

输入还不存在，就先做输入，按依赖顺序从上游往下做；不为还没做的步骤预留"输入槽"。
反例：研究计划还没有，就先派研究子代理。正确顺序是先有"问题到证据"的计划，再派人按计划找。

### 2. 验收按改动分级，不是每次都全跑 〔实践；官方规范只要求有对照，没要求每次全跑〕

改之前先定级，再决定怎么验：

| 级别 | 改了什么 | 怎么验 | 要不要基线对比 |
|---|---|---|---|
| A 不碰内容 | 加落盘、改路径文件名、加停点、改措辞 | 跑一次最便宜的命令看文件在不在、内容没被改；纯文字改动可以不跑 | 不用 |
| B 碰中间产物 | 改变某一步输出内容的规则 | 只跑到那一步，看产物文件 | 要，和上一版同级产物对比 |
| C 碰最终产物 | 改变最终交付物的规则、子代理、写手 | 跑完整流程 | 要，和最近一次 C 级基线对比 |

基线只在 B、C 级第一次改之前跑一次原版存下来，之后同级改动都拿它比，不每次重跑原版。

通过标准先写后改：改之前先把通过标准写成能打勾的清单，改的过程中不随手追加；真要追加，说明是新发现，单独记下。B、C 级先确认原版确实达不到标准（先看到失败），再动手改。〔官方规范"先建评测，再写最少的说明"；superpowers"没有先失败的测试，就不写 skill"〕

### 3. 先用最简单的做法，不够再加 〔Anthropic 文章原话：先找最简单的方案，只在需要时增加复杂度〕

能用一段说明书解决的，不派子代理；能顺序跑的，不并行；能一个命令的，不加开关；能一个文件的，不拆两个。每次想加东西先问"不加会怎样"。

### 13. 产物按下游的需要来设计 〔Anthropic 文章"每个子代理都需要目标、输出格式、工具和边界"、prompt chaining"每一步处理上一步的输出"；官方规范"可校验的中间产物"；业界文章"交接模式：输出按下游的消费方式来组织，格式在写说明书之前定"〕

第 1 条问的是"它吐什么"，这一条管"吐成什么样"。写一步的产物之前，先看下一步（和再往后的步骤）要从它拿什么，按那个需要定字段和格式：

- 下一步要用的东西，这一步给全，而且保留原样（用户原话、原始数值、来源、0 和空值分开）。
- 下一步负责的判断和加工（聚类、加总、挑代表），这一步不提前做，免得做两遍，或者做成另一种口径。
- 格式固定到脚本能读（一行一条、列名固定），交接处能用脚本校验。

下一步还没开发时，先读它的计划，写出"它要从这一步拿什么"，放进这一步的通过标准。
反例：关键词调研按"主词 / 次要词 / 长尾"分类，这是给写作塞词用的；下一步需求面要的是完整词池、每个词各自的搜索量和核心词、用户原话，好按问题聚类。按写作的口径分好类，需求面用不上，还得重做。

---

## 二、写说明书（SKILL.md）时

### 4. 只写模型不知道的 〔官方规范"默认模型已经很聪明"〕

每加一段就问：删掉它模型会做错吗？不会就删。解释常识、铺垫背景都是浪费上下文，还会稀释真正的规则。

### 5. 自由度匹配脆弱程度 〔官方规范"degrees of freedom"〕

文件名、保存路径、在哪一步停下、工具调用顺序，这些写死，只给一种做法。章节怎么组织、标题怎么起，这些给方向就行。判断标准：走错了后果严重的就写死，走错了能自己纠正的就放开。

### 6. 一层深、500 行 〔官方规范〕

SKILL.md 正文不超 500 行。细节放参考文件，SKILL.md 直接指向它；参考文件不许再指向别的参考文件。超过 100 行的参考文件顶部要有目录。参考文件没被读之前不占上下文，所以可以放得厚，但入口要清楚。

### 7. 工具写全名，给默认不列选项 〔官方规范"MCP 工具用 服务名:工具名"、"给一个默认加一个逃生口"〕

每条工具规则写成"默认用 X；X 不行时换 Y；都不行标 Z"。不列一堆可选项让模型自己挑。MCP 工具写完整名字，避免找不到。

---

## 三、验收时

### 8. 每步产物要能检查，能用脚本查就用脚本 〔官方规范"工作流写成清单 + 校验回路"、"先出计划文件、校验、再执行"〕

每一步的验收写成能打勾的话：文件在不在、有没有某一节、是否标了来源。能用脚本查的（文件存在、行数、格式）就写成脚本，让模型自己跑完再往下走。

机械步骤（复制、拼接、改名、格式转换）也交给脚本或命令，不让模型凭记忆重写一遍；模型只做需要判断的部分。〔官方规范"脚本要自己把问题解决，不推给模型"；Red Hat"机械活用脚本，判断活用模型"〕

### 9. 产物落盘，不靠转述 〔Anthropic 文章"子代理把结果写进文件系统，避免传话游戏"、"计划先存进记忆再执行"〕

子代理的结果必须写成文件，回传的是路径，不是摘要。主流程的状态也写文件。中断能续跑，出错能查到是哪一步。

### 10. 看模型怎么走，再改 〔官方规范"观察 Claude 如何在 skill 里导航"；Anthropic 文章"像你的代理一样思考"〕

每次实跑记三样：它读了哪些文件、跳过了哪一步、误用了哪个工具。改动只针对观察到的失败，不针对想象中的失败。

---

## 四、贯穿始终

### 11. 一次只改一个小功能 〔实践〕

一个功能一个分支，测完确认再合并，不同时开两个分支。改完列出"改了哪个文件、第几行、原来写什么、现在写什么"，并追加进改动日志（日期、文件行号、原来/现在、为什么、谁定的），以后能回溯。〔Red Hat"把 skill 当软件管：有版本、能审查"〕

### 12. 验收样本和环境都固定 〔实践；Anthropic 文章《Improving skill-creator》"每次测评在干净环境里跑，避免互相污染"〕

需要跑命令验收时，固定用同一个输入样本，前后才可比。样本换了，对比就失效。

验收在干净的测试目录里跑：只放运行必需的文件，不放计划、日志、笔记、验收标准。模型读到"要查什么"，测试就不是盲测了。

---

## 怎么用这份文件

不用一次记全。

- 开工前只看第 1 到 3 条和第 13 条。
- 改文件时看第 4 到 7 条。
- 验收时看第 8 到 10 条。
- 第 11、12 条是底线，随时适用。

新项目开始时，先填下面的落地表，放进项目自己的计划文件里。

## 项目落地表（每个项目填一份，放在项目计划里）

| 条目 | 项目要定的具体值 |
|---|---|
| 第 2 条 "最便宜的命令" | 例：`/blog brief` |
| 第 2 条 "完整流程" | 例：`/blog write` |
| 第 2 条 基线存放位置 | 例：`runs/<日期>-baseline/` |
| 第 6 条 行数上限 | 官方建议 500；项目 CI 若有更严的，以 CI 为准 |
| 第 11 条 改动日志位置 | 例：`docs/改动日志.md` |
| 第 12 条 固定样本 | 例：关键词 `easy costumes with normal clothes guys` |
| 第 12 条 干净测试目录 | 例：一个只放运行必需文件的独立目录 |
| 第 13 条 各步交接 | 例：每步的产物文件名、下一步从中取哪些字段 |
| 裁定登记表位置 | 例：`docs/KAI-DECISIONS.md`，K 编号 |

## 出处

- Claude Platform《Skill authoring best practices》 https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
- Anthropic《Building effective agents》 https://www.anthropic.com/engineering/building-effective-agents
- Anthropic《Effective context engineering for AI agents》 https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Anthropic《How we built our multi-agent research system》 https://www.anthropic.com/engineering/multi-agent-research-system
- Claude Code 文档《Skills》 https://code.claude.com/docs/en/skills
- Anthropic《Improving skill-creator: Test, measure, and refine Agent Skills》 https://claude.com/blog/improving-skill-creator-test-measure-and-refine-agent-skills
- MindStudio《What Is the Agent Handoff Pattern? How to Design AI Outputs for Downstream Use》 https://www.mindstudio.ai/blog/what-is-agent-handoff-pattern
- TianPan《The Output Coupling Trap: Why Multi-Agent Systems Fail Silently at Interface Boundaries》 https://tianpan.co/blog/2026/05/04/output-coupling-trap-multi-agent-systems
- obra/superpowers《writing-skills》 https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md
- Red Hat《Building skills for AI agents: pitfalls and best practices》 https://next.redhat.com/2026/07/28/building-skills-for-ai-agents-pitfalls-and-best-practices/

## 修订记录

- v1.0 2026-09-25：首版，12 条，从 claude-blog 二次开发回滚后总结。
- v1.1 2026-09-26：第 1 条删掉"留输入槽"选项；参考官方检查清单、superpowers、skill-creator、Red Hat，补 4 处：第 2 条通过标准先写后改、先看到失败；第 8 条机械步骤交给脚本；第 11 条追加改动日志；第 12 条环境也固定（干净测试目录）。条数不变。
- v1.2 2026-09-27：加第 13 条"产物按下游的需要来设计"（归"动手之前"，编号不重排）。起因：claude-blog 2-2 关键词调研按写作口径分类，没照顾下一步需求面要的输入。联网核对 Anthropic《How we built our multi-agent research system》《Building effective agents》、官方规范"可校验的中间产物"、MindStudio 交接模式、TianPan 输出耦合，方向一致。
