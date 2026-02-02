## 自己做 skills（技能）并提交到 git：所有项目复用

团队的建议：

- 如果你每天会做不止一次，就把它做成一个 skill 或 command（命令）
    
- 做一个 `/techdebt` slash command，并在每次会话结束时跑一遍，用来找出并清理重复代码
    
- 做一个 slash command，把最近 7 天的 Slack、GDrive、Asana、GitHub 同步成一份上下文 dump（上下文汇总）
    
- 像 analytics engineer（分析工程师）那样做 agents：写 dbt model、做代码 review、在 dev 环境里测试变更

## 认真维护你的CLAUDE.md

每次纠正完 Claude，都用这句话收尾：“更新你的[CLAUDE.md](https://claude.md/)

，这样你就不会再犯这个错误了。“Claude 在给自己写规则这件事上强得有点”诡异“。

随着时间推移，毫不留情地打磨你的 持续迭代，直到你能明显量化地看到 Claude 的出错率下降。

有位工程师会让 Claude 为每个任务/项目维护一个 notes 目录，每次 PR 后都更新，然后在[CLAUDE.md](https://claude.md/)  里指向这个目录。

## 每个复杂任务都从 plan mode 开始：把精力用在计划上，让 Claude 一把完成实

有人会让一个 Claude 先写 plan（计划），然后再开第二个 Claude 以 “staff engineer（资深工程师）” 的角色来审阅这份计划。

还有人说：一旦事情开始跑偏，就立刻切回 plan mode 重新规划，别硬推。

他们还会明确告诉 Claude：验证步骤也要进入 plan mode，不只是写代码时才用。

## <mark style="background: #FF5582A6;">大多数 bug Claude 能自己修：我们是这么做的</mark>

启用 Slack MCP，然后把 Slack 里的 bug 讨论串贴给 Claude，只说一句 “fix”。完全不需要切来切去。

或者直接说：“去修 failing 的 CI tests。”别告诉它怎么修，别微操。

把 Claude 指向 docker logs 来排查分布式系统问题——它在这方面强得出乎意料。

## <mark style="background: #FF5582A6;">提示词进阶</mark>

a. 质疑 Claude：比如说“对这些改动狠狠审问我，只有我通过你的测试你才准发 PR。”让 Claude 当你的 reviewer。或者说：“证明给我看这能跑”，让它比较 main 分支和你的 feature 分支的行为差异（diff 行为）。

b. 当修得一般般时，直接说：“基于你现在已经知道的一切，把这套方案扔掉，重新实现一个优雅的解法。”

c. 交付前先写清楚规格说明（spec），尽量减少歧义。你越具体，输出越好。

## <mark style="background: #FFF3A3A6;">用 Claude 学习</mark>

团队还有一些用 Claude Code 学习的技巧：

a. 在 `/config` 里启用 “Explanatory” 或 “Learning” 输出风格，让 Claude 解释它改动背后的 *why*。

b. <mark style="background: #FFB86CA6;">让 Claude 生成一个可视化的 HTML presentation 来讲解陌生代码——它做出来的幻灯片意外地好</mark>。

c. 让 Claude 用 ASCII 画协议/代码库结构图，帮助你快速理解。

d. 做一个 spaced-repetition（间隔复习）学习 skill：你先讲自己的理解，Claude 追问补缺口，并把结果存下来。