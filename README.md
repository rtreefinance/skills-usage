# Claude Code Skills 使用手册

> 最后更新：2026-05-22 | 共 29 个 Skills（12 Plugin + 17 Slash Command）

---

## 快速索引

| 我想做的事 | 用这个 |
|-----------|--------|
| 开发一个复杂功能，需要规划和拆解 | [superpowers](#superpowers) |
| 审查 PR / 代码质量 | [code-review](#code-review)、[crg-review-pr](#code-review-graph) |
| 提交代码 / 推送 / 创建 PR | [commit-commands](#commit-commands) |
| 做界面设计或前端实现 | [frontend-design](#frontend-design) |
| 金融建模、估值、行业分析 | [financial-analysis](#financial-analysis) |
| 让 Claude 记住跨会话的上下文 | [agentmemory](#agentmemory) |
| 整理 Obsidian 笔记 | [obsidian](#obsidian) |
| 强制用最简单的方案解决问题 | [caveman](#caveman) |
| 把任务计划写进文件追踪进度 | [planning-with-files](#planning-with-files) |
| 大型项目全生命周期管理 | [ecc](#ecc) |
| 监控 token 用量和会话状态 | [claude-hud](#claude-hud) |
| 让 Codex 协助审查或执行任务 | [codex](#codex) |
| 搜索发现新 Skill | [/find-skills](#find-skills) |
| 从代码文件生成编码规范 | [/write-coding-standards-from-file](#write-coding-standards-from-file) |
| 操控浏览器（本机） | [/browser-use](#browser-use) |
| 操控浏览器（云端/CI） | [/browser-use-cloud](#browser-use-cloud--browser-use-remote-browser) |
| 用 Python 写浏览器自动化代码 | [/browser-use-open-source](#browser-use-open-source) |
| 生成 Excalidraw 图表 | [/excalidraw](#excalidraw) |
| 整理内容给 NotebookLM | [/notebooklm](#notebooklm) |
| 把 AI 文字改写得更自然 | [/humanizer](#humanizer) |
| 生成 PowerPoint 文件 | [/pptx](#pptx) |
| 分析代码依赖图 / 重构 | [/crg-*](#code-review-graph) |

---

## Plugin 类 Skills

> Plugin 安装后自动生效，无需手动调用命令。

---

### superpowers
**来源：** `anthropics/claude-plugins-official` | **版本：** 5.1.0

**用途：** 让 Claude 像资深工程师一样工作——先规划、先写测试、再实现，适合一切有一定复杂度的开发任务。

**什么时候用：**
- 要开发一个新功能，不知道从哪里下手
- 需要拆解复杂任务，交给多个 subagent 并行执行
- 想用 TDD 方式保证代码质量
- 开发完一个分支，需要整理收尾

**如何触发（直接说给 Claude 听）：**
```
"帮我规划一下这个功能的实现方案"       → writing-plans
"按照计划开始执行"                      → executing-plans
"用 TDD 的方式实现这个功能"             → test-driven-development
"把这个任务拆分给多个 agent 并行处理"   → dispatching-parallel-agents
"这个分支开发完了，帮我收尾"            → finishing-a-development-branch
"这个 bug 排查一下"                     → systematic-debugging
"帮我做代码审查"                        → requesting-code-review
```

**包含子 Skills：** brainstorming、writing-plans、executing-plans、test-driven-development、subagent-driven-development、dispatching-parallel-agents、systematic-debugging、requesting-code-review、receiving-code-review、finishing-a-development-branch、using-git-worktrees、verification-before-completion、writing-skills、using-superpowers

---

### code-review
**来源：** `anthropics/claude-plugins-official`

**用途：** 多个专项 Agent 协作对 PR 进行全面审查，输出置信度评分和改进建议。

**什么时候用：**
- 提交 PR 之前想自查一遍
- 想检查安全漏洞、性能问题、代码规范问题

**如何触发：**
```
"帮我 review 这个 PR"
"审查一下当前的代码改动"
"这段代码有没有安全问题"
```

---

### commit-commands
**来源：** `anthropics/claude-plugins-official`

**用途：** 简化日常 Git 操作，自动分析变更生成规范 commit message。

**如何触发：**

| 命令 | 说法 | 效果 |
|------|------|------|
| `/commit` | "帮我提交代码" | 分析暂存区，生成 commit message，执行提交 |
| `/commit-push-pr` | "提交并推送，创建 PR" | 一键 commit → push → 创建 Pull Request |
| `/clean_gone` | "清理废弃的本地分支" | 删除远端已不存在的本地追踪分支 |

**注意：** 执行前确保已 `git add` 暂存需要提交的文件。

---

### frontend-design
**来源：** `anthropics/claude-plugins-official`

**用途：** 生产级前端 UI 实现，具备高设计标准，输出的界面美观且规范。

**什么时候用：**
- 实现页面布局、UI 组件
- 需要应用设计系统（颜色、字体、间距）
- 响应式设计、无障碍（a11y）适配

**如何触发：**
```
"帮我实现这个页面的 UI"
"参考这个设计稿，写出对应的组件"
"优化一下这个按钮/卡片/表单的样式"
```

---

### financial-analysis
**来源：** `anthropics/financial-services` | **版本：** 0.1.1

**用途：** 专业金融建模与分析，集成 Morningstar、PitchBook、S&P Global 等 11 个数据源。

**什么时候用 / 如何触发：**

| 场景 | 说法 |
|------|------|
| 现金流折现估值 | "帮我做一个 DCF 模型" |
| 杠杆收购分析 | "做一个 LBO 模型" |
| 可比公司分析 | "找 3-5 个可比公司做 comps 分析" |
| 三表联动模型 | "建一个三表财务模型" |
| 竞争对手分析 | "分析一下这个行业的竞争格局" |
| 路演材料审核 | "检查一下这个 deck 的数据和逻辑" |
| Excel 数据清洗 | "清洗这份 Excel 里的财务数据" |
| PPT 更新 | "用最新数据更新这份路演材料" |

**集成数据源：** Morningstar、S&P Global、FactSet、PitchBook、LSEG、Moody's、Aiera、Daloopa、Chronograph、MT Newswire、Egnyte

---

### agentmemory
**来源：** `rohitg00/agentmemory` | **版本：** 0.9.21

**用途：** 为 Claude 提供跨会话持久记忆，自动记录工具使用和关键上下文，下次打开会话时仍能记住。

**什么时候用：**
- 长期持续的项目，不想每次重新交代背景
- 希望 Claude 记住你的偏好和决策习惯

**如何触发：** 自动运行，无需手动调用。Claude 会在后台捕获和压缩记忆。

---

### obsidian
**来源：** `kepano/obsidian-skills` | **版本：** 1.0.1

**用途：** 专为 Obsidian 知识库设计，帮助管理笔记、双链和知识图谱。

**什么时候用：**
- 整理 Obsidian vault 的笔记结构
- 生成笔记模板
- 查找和维护笔记之间的链接关系

**如何触发：**
```
"在 Obsidian 里创建一个新笔记"
"整理一下我的笔记双链"
"生成一个 [主题] 的笔记模板"
```

---

### caveman
**来源：** `JuliusBrussee/caveman`

**用途：** 强制 Claude 用最简单、最直接的方案解决问题，防止过度工程化。

**什么时候用：**
- 你感觉 Claude 的方案太复杂了
- 想要一个快速可用的 MVP，不需要完美架构
- 原型验证阶段，速度优先

**如何触发：**
```
"用最简单的方式实现这个功能"
"caveman 模式：我只要能跑起来，不要复杂"
"不要过度设计，给我最直接的解法"
```

---

### planning-with-files
**来源：** `OthmanAdi/planning-with-files` | **版本：** 2.40.0

**用途：** 把任务计划写入 Markdown 文件，持久化追踪执行进度，避免计划只存在于对话中丢失。

**什么时候用：**
- 任务比较长，需要多次会话才能完成
- 想把计划保存下来，随时查看进度
- 多步骤任务需要明确的 checkpoint

**如何触发：**
```
"把这个任务的计划写进文件"
"创建一个实现计划并保存"
"按照计划文件继续执行下一步"
```

---

### ecc
**来源：** `affaan-m/ECC` | **版本：** 2.0.0-rc.1

**用途：** 生产级 AI 编码插件，内置 60 个专项 Agent、232 个 Skills、75 个命令，覆盖软件开发全生命周期。安装后 Claude 会**主动**在合适时机调用对应 Agent，无需每次手动触发。

---

#### 自动触发规则（Claude 主动调用）

| 场景 | 自动调用的 Agent |
|------|----------------|
| 收到复杂功能请求 | `planner` |
| 刚写完或修改了代码 | `code-reviewer` |
| 修 bug 或开发新功能 | `tdd-guide` |
| 需要做架构决策 | `architect` |
| 涉及安全敏感代码 | `security-reviewer` |
| 运行自主循环任务 | `loop-operator` |

---

#### 专项 Agent 完整列表（60 个，按领域分）

**核心开发流程：**

| 说法 / 场景 | Agent |
|-------------|-------|
| "帮我规划这个功能" / 复杂功能、重构 | `planner` |
| "设计一下系统架构" / 架构决策 | `architect` |
| "用 TDD 方式实现" / 新功能或 bug 修复 | `tdd-guide` |
| "review 一下这段代码" | `code-reviewer` |
| "检查安全漏洞" / 提交前、敏感代码 | `security-reviewer` |
| "build 挂了帮我看看" | `build-error-resolver` |
| "清理死代码 / 重构" | `refactor-cleaner` |
| "更新文档" | `doc-updater` |
| "跑 E2E 测试" | `e2e-runner` |
| "查 API 文档" | `docs-lookup` |

**语言专项 reviewer：**

| 语言 | Reviewer Agent | Build Resolver |
|------|---------------|----------------|
| TypeScript / JavaScript | `typescript-reviewer` | — |
| Python | `python-reviewer` | — |
| Go | `go-reviewer` | `go-build-resolver` |
| Rust | `rust-reviewer` | `rust-build-resolver` |
| Java / Spring Boot | `java-reviewer` | `java-build-resolver` |
| Kotlin / Android | `kotlin-reviewer` | `kotlin-build-resolver` |
| C / C++ | `cpp-reviewer` | `cpp-build-resolver` |
| F# | `fsharp-reviewer` | — |
| Django | `django-reviewer` | `django-build-resolver` |
| ML / PyTorch | `mle-reviewer` | `pytorch-build-resolver` |
| PostgreSQL / Supabase | `database-reviewer` | — |

**自主运行类：**

| 说法 / 场景 | Agent |
|-------------|-------|
| 自主循环任务监控 | `loop-operator` |
| 调优 harness 配置 | `harness-optimizer` |

---

#### Slash Commands（3 个）

| 命令 | 用途 | 用法示例 |
|------|------|---------|
| `/feature-development` | 标准功能开发工作流：理解现状 → 最小化改动 → 验证 → 总结 | "用 feature-development 流程实现这个功能" |
| `/database-migration` | 数据库 schema 变更工作流：创建迁移文件 → 更新 schema → 生成类型 | "按 database-migration 流程做这次 schema 变更" |
| `/add-language-rules` | 为项目新增编程语言规范（coding-style、hooks、patterns、security、testing） | "给项目添加 Python 语言规范" |

---

#### 核心编码原则（ECC 强制要求）

- **TDD 强制**：先写测试（RED）→ 最小实现（GREEN）→ 重构（80%+ 覆盖率）
- **不可变性**：永远创建新对象，不修改现有对象
- **安全优先**：提交前必须检查 secrets、SQL 注入、XSS、CSRF
- **文件大小**：单文件 200-400 行，上限 800 行，函数 < 50 行
- **Conventional Commits**：`feat:`、`fix:`、`refactor:`、`docs:`、`test:`、`chore:`

---

#### 开发工作流（ECC 推荐顺序）

```
1. planner     → 规划，识别依赖和风险，拆分阶段
2. tdd-guide   → 先写测试，再实现，再重构
3. code-reviewer → 立即 review，处理 CRITICAL/HIGH 问题
4. security-reviewer → 提交前安全检查
5. commit      → conventional commits 格式 + 完整 PR 摘要
```

---

### claude-hud
**来源：** `jarrodwatts/claude-hud`

**用途：** 在会话中实时显示 context 使用量、活跃工具、运行中 Agent 和 token 消耗，帮助掌握会话状态。

**什么时候用：**
- 长会话中想知道还剩多少 context 空间
- 监控 token 成本
- 调试多 Agent 并行任务时追踪状态

**如何触发：** 安装后自动显示，无需手动调用。

---

### codex
**来源：** `openai/codex-plugin-cc`

**用途：** 在 Claude Code 中调用 OpenAI Codex，将代码审查或特定任务委派给 Codex 执行，实现双模型协作。

**什么时候用：**
- 想用 Codex 的视角交叉验证 Claude 的代码
- 特定编程任务希望对比两个模型的输出

**如何触发：**
```
"用 Codex 审查一下这段代码"
"把这个任务交给 Codex 处理"
```

---

## Slash Command 类 Skills

> 通过 `/命令名` 在对话中调用，文件存于 `~/.claude/commands/`。

---

### find-skills
**文件：** `find-skills.md` | **来源：** `vercel-labs/skills`
**调用：** `/find-skills`

**用途：** 在 skills.sh 生态中搜索和推荐 Agent Skills。

**什么时候用：** 想找某类能力的 skill，但不知道叫什么名字。

**用法：**
```
/find-skills
→ 然后描述你需要什么能力，Claude 会搜索 skills.sh 给出推荐

# 或者直接在终端搜索
npx skills find react performance
npx skills find pr review
npx skills add vercel-labs/agent-skills@react-best-practices -g -y
```

---

### write-coding-standards-from-file
**文件：** `write-coding-standards-from-file.md` | **来源：** `github/awesome-copilot`
**调用：** `/write-coding-standards-from-file <文件或文件夹>`

**用途：** 分析现有代码的风格，自动生成该项目的编码规范文档。

**什么时候用：** 项目缺少编码规范，想从现有代码中提炼出来。

**用法：**
```
/write-coding-standards-from-file src/main.py
→ 分析单个文件，生成规范文档

/write-coding-standards-from-file src/
→ 分析整个目录，聚合后生成

/write-coding-standards-from-file src/ useTemplate=minimal
→ 用简洁模板输出

/write-coding-standards-from-file src/app.ts addToREADME=true
→ 直接追加到 README.md
```

**输出文件（按优先级自动选择）：** `CONTRIBUTING.md` → `STYLE.md` → `CODING_STANDARDS.md` → `GUIDELINES.md`

---

### browser-use
**文件：** `browser-use.md` | **来源：** `browser-use/browser-use`
**调用：** `/browser-use`

**用途：** 在本机自动化操作浏览器，约 50ms 延迟（常驻 daemon）。

**什么时候用：** 需要自动填表、网页数据抓取、截图、测试 web 功能。

**用法：**
```bash
# 标准工作流：先 open，再 state 看元素，再操作
browser-use open https://example.com
browser-use state                          # 查看可交互元素和编号（必须先运行）
browser-use click 3                        # 点击编号为 3 的元素
browser-use input 5 "admin@example.com"   # 在编号 5 的输入框输入
browser-use screenshot                     # 截图确认结果
browser-use close                          # 关闭会话

# 使用已登录的 Chrome（保留 cookies）
browser-use connect
browser-use open https://gmail.com

# 连接指定 Chrome Profile
browser-use profile list
browser-use --profile "Default" open https://github.com
```

---

### browser-use-cloud / browser-use-remote-browser
**文件：** `browser-use-cloud.md` / `browser-use-remote-browser.md`
**来源：** `browser-use/browser-use`
**调用：** `/browser-use-cloud` / `/browser-use-remote-browser`

**用途：**
- `cloud`：调用 Browser Use 云端 API（v2/v3），无需本地浏览器，支持代理、CAPTCHA 处理
- `remote-browser`：在沙盒/CI/云端 VM 等无 GUI 环境中控制浏览器

**用法（cloud）：**
```bash
browser-use cloud login <api-key>          # 保存 API Key
browser-use cloud connect                  # 启动云端浏览器并连接
browser-use open https://example.com       # 后续操作与本地相同
```

**用法（remote，多 Agent 共享浏览器）：**
```bash
INDEX=$(browser-use register)              # 注册 agent，获得 tab 索引
browser-use --connect $INDEX open <url>   # 在专属 tab 中操作
browser-use tunnel 3000                    # 把本地 3000 端口暴露给浏览器访问
```

---

### browser-use-open-source
**文件：** `browser-use-open-source.md` | **来源：** `browser-use/browser-use`
**调用：** `/browser-use-open-source`

**用途：** 用 Python 代码调用 browser-use 库开发 AI 浏览器 Agent 的参考文档。

**什么时候用：** 需要写 Python 脚本，而不是用 CLI 命令操控浏览器。

**用法：**
```python
# 安装
# uv pip install browser-use && uvx browser-use install

from browser_use import Agent
import asyncio

async def main():
    agent = Agent(task="打开百度搜索 Claude Code")
    await agent.run()

asyncio.run(main())
```

---

### excalidraw
**文件：** `excalidraw.md` | **来源：** `yctimlin/mcp_excalidraw`
**调用：** `/excalidraw`

**用途：** 让 Claude 生成和编辑 Excalidraw 图表（架构图、流程图、思维导图等）。

**什么时候用：** 需要可视化系统架构、流程、数据结构。

**用法：**
```
/excalidraw
→ 描述你想画的图，Claude 生成 Excalidraw JSON

"画一个三层架构图：前端、后端、数据库"
"把这个用户注册流程画成流程图"
"画一个 React 组件的状态管理关系图"
```

---

### notebooklm
**文件：** `notebooklm.md` | **来源：** `PleasePrompto/notebooklm-skill`
**调用：** `/notebooklm`

**用途：** 将内容整理为适合 NotebookLM 使用的格式，辅助知识提炼和深度研究。

**什么时候用：** 有大量资料需要整理，准备导入 NotebookLM 做进一步分析。

**用法：**
```
/notebooklm
→ 粘贴或描述你的资料内容，Claude 帮你结构化整理

"把这篇研究报告整理成 NotebookLM 格式"
"把这几个会议记录合并整理，准备导入 NotebookLM"
```

---

### humanizer
**文件：** `humanizer.md` | **来源：** `blader/humanizer`
**调用：** `/humanizer`

**用途：** 将 AI 生成的文字改写得更自然、流畅，减少 AI 腔调。

**什么时候用：** 用 Claude 写的文案、邮件、报告读起来太"AI味"，需要更像人写的。

**用法：**
```
/humanizer
→ 把需要改写的文字发给 Claude

"把这段产品介绍改写得更自然"
"这封邮件太正式了，帮我改得口语化一点"
"这篇报告的总结 AI 味太重，humanize 一下"
```

---

### pptx
**文件：** `pptx.md` | **来源：** `anthropics/skills`
**调用：** `/pptx`

**用途：** 直接生成 `.pptx` PowerPoint 文件，支持结构化幻灯片内容输出。（Anthropic 官方出品）

**什么时候用：** 需要输出一份真正可用的 PPT 文件，而不只是 Markdown 大纲。

**用法：**
```
/pptx
→ 描述 PPT 的主题和内容结构

"做一个 10 页的融资路演 PPT，主题是 [公司名]"
"把这份分析报告转成 PPT 格式，8 张幻灯片"
"生成一个季度业绩汇报的 PPT 框架"
```

---

### code-review-graph
**文件：** `crg-*.md`（7 个）| **来源：** `tirth8205/code-review-graph`

**用途：** 基于代码依赖图分析的代码审查和重构工具集，从图结构角度理解代码。

**7 个子命令：**

| 命令 | 用途 | 用法示例 |
|------|------|---------|
| `/crg-build-graph` | 构建代码依赖图，可视化模块关系 | "为这个项目构建依赖图" |
| `/crg-explore-codebase` | 图式探索代码库，快速理解陌生项目 | "帮我了解这个项目的结构" |
| `/crg-review-pr` | PR 级别全面代码审查 | "审查这个 PR 的所有改动" |
| `/crg-review-changes` | 针对当前变更的审查 | "检查我刚写的这些改动" |
| `/crg-review-delta` | 增量差异对比审查 | "对比上个版本，分析新增的问题" |
| `/crg-debug-issue` | 利用依赖图定位 bug 根源 | "这个 bug 影响了哪些模块" |
| `/crg-refactor-safely` | 分析影响范围，给出安全重构建议 | "我想重构这个模块，有哪些风险" |

---

## 管理参考

### 安装新 Plugin
```bash
# 从官方/已有 marketplace 安装
claude plugin install <name>

# 添加新 marketplace 并安装
claude plugin marketplace add <owner/repo>
claude plugin install <name>@<marketplace>
```

### 安装新 Slash Command（SKILL.md）
```bash
gh api repos/<owner>/<repo>/contents/<path>/SKILL.md \
  --jq '.content' | base64 -d > ~/.claude/commands/<name>.md
```

### 常用管理命令
```bash
claude plugin list                        # 查看所有已安装 plugin
claude plugin disable <name>              # 临时禁用（节省 token）
claude plugin enable <name>               # 重新启用
claude plugin update <name>               # 更新
claude plugin marketplace update          # 更新所有 marketplace 索引
ls ~/.claude/commands/                    # 查看所有 slash commands
```

### Token 消耗参考

| Skill | 常驻消耗 |
|-------|---------|
| financial-analysis | ~1,282 tok |
| superpowers | ~484 tok |
| commit-commands | ~76 tok |
| frontend-design | ~59 tok |
| code-review | ~19 tok |

> token 紧张时优先禁用 `financial-analysis`，再考虑 `superpowers`。
