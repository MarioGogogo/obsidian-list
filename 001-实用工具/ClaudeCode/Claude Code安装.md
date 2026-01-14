<!--
 * @Author: MarioGogogo 148373644@qq.com
 * @Date: 2026-01-07 14:18:56
 * @LastEditors: MarioGogogo 148373644@qq.com
 * @LastEditTime: 2026-01-13 12:01:39
 * @FilePath: /obsidian-list/001-实用工具/ClaudeCode/Claude Code安装.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->

仓库：[https://github.com/UfoMiao/zcf](https://link.segmentfault.com/?enc=ylV7Ga4L2jE3NttMZO%2BQXQ%3D%3D.rJr1mAZUfUj5bKzlOzNulWS3FYUKRX8rYn9UeAfq%2FJk%3D)  
找到好用的 prompt 会持续更新的

> 安装方法可以参考仓库的[README.md](https://link.segmentfault.com/?enc=q2432fId4MF5%2FYfUetn14A%3D%3D.bikbRWJSiuKY8mGphbYw2QuxF2tUM%2FbbgLuw%2B2End1dV7vdF3PZEj1P89FCtLodHAuWHnBJVN%2BTv1jdA%2FGSH0Q%3D%3D)，**建议使用英文的版本，可以减少点 token 消耗**

## 使用指北

### 根据你的情况选择：

#### 🆕 首次使用 Claude Code

```bash
npx zcf          # 完整初始化：安装 Claude Code + 导入工作流 + 配置 API + 设置 MCP 服务
```

#### 🔄 已有 Claude Code 环境

```bash
npx zcf u        # 仅导入工作流：快速添加 AI 工作流和命令系统
```

**项目第一次使用强烈建议先运行  `/init`  进行初始化总结出 CLAUDE.md，便于 AI 理解项目架构**

- `<任务描述>` - <mark style="background:#ff4d4f">不使用任何工作流直接执行，会遵循 SOLID、KISS、DRY 和 YAGNI 原则</mark>，适合做小功能和修复 Bug 等小任务
- `/feat <任务描述>` - <mark style="background:#d4b106">开始新功能开发，分为 plan 和 ui 两个阶段</mark>
- `/workflow <任务描述>` - 执行完整开发工作流，不是自动化，<mark style="background: #2ADF39;">开始会给出多套方案，每一步会询问用户意见，可随时修改方案</mark>，掌控力 MAX

> - feat 和 workflow 这两套各有优势，佬友们可以都试试比较一下
> - 生成的文档位置默认都是项目根目录下的.claude/xxx.md，可以把`.claude/`加入项目的`.gitignore`里

## 工具使用效果图

![](assets/Claude%20Code安装/file-20260108152115754.png)

## 碎碎念：

这段时间试了各种提示词，重点体验的有 superClaude 还有最近的 spec

- superClaude 使用起来太麻烦，还要记命令和参数，效果也一般；
- spec 试了几套发现都不太行，跟不少佬友的体验一样，是个版本陷阱，又慢效果也不行，开头写了一堆 markdown，写出来的代码太啰嗦，实现的功能还不行
- 项目 99% 代码都是这套工作流写出来的，我只微调了文案啥的
- 另外三个字母的 npm 真难找，让 cc 查了好多才找到个像样的，一开始找到个 zcc，以为没人用，结果是被人注册但是没有用

## 参考博客

https://adocomplete.com/advent-of-claude-2025/
