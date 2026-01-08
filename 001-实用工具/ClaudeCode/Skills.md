# Claude Code Skills

![Skills Dashboard](assets/Skills/file-20260108152217277.png)

**官网链接**：[https://skillsmp.com/](https://skillsmp.com/)

<font color="#ff0000">Skills 是 Claude Code 的插件系统。每个 Skill 都是一个可复用的工具模块，自动处理特定的开发任务，帮你节省大量重复工作。</font>

---

## 为什么使用 Skills

### 提升效率

将多步骤操作压缩成一条命令：

```
传统方式：编写代码 → 格式化 → 添加文档 → 提交 Git（4个步骤）
使用 Skills：/commit（1个命令）
```

平均节省 **60-80%** 的重复工作时间。

### 内置最佳实践

每个 Skill 都封装了行业标准：

- **代码规范**：自动遵循项目代码风格
- **设计模式**：应用成熟的架构模式
- **安全标准**：内置 OWASP 安全检查
- **性能优化**：基于实际场景的优化策略

### 工具链集成

- Git 工作流：自动化分支管理、代码审查
- IDE 集成：与 VSCode、JetBrains 配合
- CI/CD 管道：支持持续集成和部署
- 文档生成：自动维护项目文档

---

## 推荐的 Skills

### frontend-design

生成生产级的前端界面设计。

**适用场景：**

- 快速原型开发
- Landing Page 设计
- 管理后台界面
- 移动端应用界面
- 组件库搭建

**使用示例：**

```
请帮我设计一个现代化的登录页面，包含：
- 邮箱/用户名输入框
- 密码输入框（带显示/隐藏切换）
- 记住我复选框
- 忘记密码链接
- 社交登录按钮（Google、GitHub）
- 响应式布局，适配移动端
```

**技术特点：**

| 特性 | 说明 |
|------|------|
| 设计质量 | 避免 AI 通用模板感，确保独特性 |
| 组件封装 | 高度可复用的组件架构 |
| 主题系统 | 完整的颜色、字体、间距设计规范 |
| 移动优先 | Mobile-first 的响应式设计 |
| 可访问性 | 遵循 WCAG 无障碍标准 |

---

### planning-with-files

基于文件的项目规划和任务管理系统。

**核心功能：**

- 将计划和知识存储在 Markdown 文件中
- 可视化项目进度和任务状态
- 构建项目知识库，支持长期回顾
- Manus 风格的标准化文档格式
- 将复杂项目拆解为可管理的子任务

**适用场景：**

- 复杂功能开发前的规划
- 多步骤项目的任务管理
- 技术调研和可行性分析
- 代码重构策略制定
- 团队知识共享和文档化

**文档结构：**

```
project-plan/
├── 01-research.md          # 调研阶段
├── 02-architecture.md      # 架构设计
├── 03-implementation.md    # 实施计划
├── 04-testing.md          # 测试策略
├── 05-deployment.md       # 部署方案
└── 00-index.md            # 总览索引
```

---

### readme-writing

自动生成标准化的项目文档。

**核心功能：**

- 遵循业界最佳实践的 README 结构
- 基于项目代码自动生成基础内容
- 美观的 Markdown 排版和徽章系统
- 支持国际化项目的多语言文档

**标准结构：**

```markdown
# 项目名称

简短有力的项目描述

## 功能特性
- 特性 1
- 特性 2

## 快速开始
```bash
npm install your-project
```

## 截图演示
![项目截图](screenshot.png)

## 贡献指南
欢迎提交 Pull Request

## 许可证
MIT License
```

**最佳实践：**

1. 使用简洁有力的项目名称和描述
2. 添加项目截图或演示 GIF
3. 提供 5 分钟内可运行的最小示例
4. 与代码变更同步更新文档
5. 设置常见问题解答

---

## 如何选择合适的 Skill

根据任务类型选择：

| 任务类型 | 推荐技能 | 原因 |
|---------|---------|------|
| UI 界面设计 | `frontend-design` | 生产级设计质量 |
| 项目规划 | `planning-with-files` | 持久化和可追溯 |
| 文档编写 | `readme-writing` | 标准化格式 |
| Git 操作 | `zcf:git-commit` | 智能提交信息 |
| 代码审查 | `review-pr` | 自动化 PR 检查 |

---

## 使用技巧

### 组合使用

```
规划阶段：/planning-with-files
开发阶段：/frontend-design
提交阶段：/git-commit
文档阶段：/readme-writing
```

### 优化参数

- 为 Skill 提供清晰的上下文信息
- 指定具体的输出格式要求
- 说明项目的技术栈和约束条件

### 迭代改进

- 保存满意的 Skill 输出作为模板
- 根据项目特点定制 Skill 参数
- 建立团队的 Skill 使用规范

---

## 注意事项

**应该做的：**

- 始终检查 Skill 生成的代码和文档
- 将 Skill 生成的文件纳入 Git 管理
- 确保团队对 Skill 使用有一致理解

**不应该做的：**

- 过度依赖 Skill，不能完全替代人工判断
- 盲目信任输出，安全关键代码必须人工审查

---

## 效果对比

| 指标 | 传统方式 | 使用 Skills | 提升 |
|------|---------|------------|------|
| README 编写 | 30-60 分钟 | 5-10 分钟 | 5-6x |
| UI 原型设计 | 2-4 小时 | 20-30 分钟 | 4-8x |
| Git 提交信息 | 2-5 分钟 | 10 秒 | 12-30x |
| 项目规划文档 | 1-2 天 | 1-2 小时 | 8-12x |

**代码质量改善：**

- 代码风格和结构更加统一
- 文档覆盖率从 30% 提升到 90%+
- 自动应用行业标准和设计模式
- 清晰的代码结构降低维护成本

---

## 开始使用

```bash
# 初始化 Claude Code
npm install -g @anthropic-ai/claude-code

# 配置 Skills
claude-code skills install frontend-design
claude-code skills install planning-with-files
claude-code skills install readme-writing

# 开始使用
claude-code
```

---

## 相关资源

- **Skills 市场**：[https://skillsmp.com/](https://skillsmp.com/)
- **Claude Code 文档**：[https://docs.anthropic.com/](https://docs.anthropic.com/)
- **GitHub 仓库**：[https://github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)

---

*最后更新：2026-01-08*
