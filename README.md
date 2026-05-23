# Claude Code Skills 使用手册

> 最后更新：2026-05-23 | 共 31 个 Skills（12 Plugin + 19 Slash Command）

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
| 压缩回复长度，减少 token 消耗 | [caveman](#caveman) |
| 把任务计划写进文件追踪进度 | [planning-with-files](#planning-with-files) |
| 大型项目全生命周期管理 | [ecc](#ecc) |
| 监控 token 用量和会话状态 | [claude-hud](#claude-hud) |
| 把任务委派给 Codex CLI 执行 | [codex](#codex) |
| 搜索发现新 Skill | [/find-skills](#find-skills) |
| 从代码文件生成编码规范 | [/write-coding-standards-from-file](#write-coding-standards-from-file) |
| 操控浏览器（本机） | [/browser-use](#browser-use) |
| 操控浏览器（云端/CI） | [/browser-use-cloud](#browser-use-cloud--browser-use-remote-browser) |
| 用 Python 写浏览器自动化代码 | [/browser-use-open-source](#browser-use-open-source) |
| 生成 Excalidraw 图表 | [/excalidraw](#excalidraw) |
| 浏览器自动化操控 NotebookLM | [/notebooklm](#notebooklm) |
| 把 AI 文字改写得更自然 | [/humanizer](#humanizer) |
| 生成 PowerPoint 文件 | [/pptx](#pptx) |
| 分析代码依赖图 / 重构 | [/crg-*](#code-review-graph) |
| 生成优化的多阶段 Dockerfile | [/multi-stage-dockerfile](#multi-stage-dockerfile) |
| shadcn/ui 组件管理 | [shadcn](#shadcn)（自动触发） |

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

**用途：** 启动多个并行 Agent 从不同维度审查 PR，用置信度分数（0-100）过滤误报，≥80 的问题才会评论到 PR。

**实际工作流：**
1. **Haiku 预检**：PR 是否关闭/草稿/已审查/不需要审查
2. **5 个 Sonnet Agent 并行独立审查：**
   - Agent 1：CLAUDE.md 合规检查
   - Agent 2：浅层 bug 扫描（仅看变更行）
   - Agent 3：`git blame` + 历史上下文中的 bug
   - Agent 4：历史 PR 评论中的相关问题
   - Agent 5：代码注释的合规性
3. **Haiku 置信度评分**（0/25/50/75/100），过滤 <80 分的误报
4. **`gh` 命令评论**到 PR，附带完整 SHA 文件链接

**如何触发：**
```
"帮我 review 这个 PR"
"审查一下当前的代码改动"
/code-review <PR号>
```

**注意：** 不检查 build/type-check（由 CI 负责），不留 emoji，评论简短。

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

**用途：** 生产级前端 UI 实现，在写代码之前先确立**大胆的设计方向**，避免输出千篇一律的"AI 味"界面。

**核心理念：**
- **方向先于代码**：在实现前选定设计风格（极简主义/野兽派/极大化/企业风…），不接受"视主题而定"的模糊回答
- **刻意出人意料**：排版、颜色、间距有意选择意外之选，每个决策都要有创意理由
- **拒绝 AI Slop 特征**：不用 Inter/Roboto 字体，不用紫色渐变，不用空洞 placeholder，不用圆角 card + 漂浮阴影
- **风格一致**：全页面提交一种审美，不做折中

**什么时候用：**
- 实现页面布局、UI 组件
- 需要应用设计系统（颜色、字体、间距）
- 响应式设计、无障碍（a11y）适配

**如何触发：**
```
"帮我实现这个页面的 UI"
"参考这个设计稿，写出对应的组件"
"优化一下这个按钮/卡片/表单的样式"
"做一个有独特风格的登录页"
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

**用途：** 为 Claude 提供跨会话持久记忆，使用 `memory_save` 和 `memory_smart_search` MCP 工具主动存储和检索记忆。

**什么时候用：**
- 长期持续的项目，不想每次重新交代背景
- 希望 Claude 记住你的偏好和决策习惯
- 多会话任务需要传递上下文

**如何触发（两种方式）：**

**方式一：显式命令**
```
/remember 我们决定用 PostgreSQL 而不是 MongoDB，原因是查询复杂度
→ 调用 memory_save 工具存储

/recall 上次我们选的数据库是什么
→ 调用 memory_smart_search 检索
```

**方式二：自然语言**
```
"记住我们今天的架构决策"
"帮我保存一下这个 API Key 的用途"
"你还记得上次我们讨论的认证方案吗"
```

**包含子技能：**
- `remember`：显式存储记忆
- `recall`：检索记忆
- `commit-context`：提交时自动保存上下文
- `handoff`：会话交接时总结当前状态
- `recap`：回顾历史记忆摘要

---

### obsidian
**来源：** `kepano/obsidian-skills` | **版本：** 1.0.1

**用途：** 通过 `obsidian` CLI 直接操作 Obsidian vault，管理笔记、双链、日记、标签、任务等。

**前提：** 必须已安装 `obsidian` CLI，且 Obsidian 应用正在运行。

**笔记操作命令：**
```bash
obsidian read "Note Title"                    # 读取笔记内容
obsidian create "Note Title" "内容"           # 新建笔记
obsidian append "Note Title" "追加内容"       # 向笔记末尾追加
obsidian search "关键词"                      # 搜索 vault

obsidian daily:read                           # 读取今天的日记
obsidian daily:append "今天完成了..."          # 向今日日记追加内容

obsidian property:set "Note" "status" "done"  # 设置 frontmatter 属性
obsidian tasks "Note Title"                   # 列出笔记中的任务
obsidian tags "Note Title"                    # 列出标签
obsidian backlinks "Note Title"               # 查看反向链接
```

**插件开发支持：**
```bash
obsidian plugin:reload "plugin-id"            # 热重载插件
obsidian dev:errors                           # 查看开发者控制台错误
obsidian dev:screenshot                       # 截图当前 Obsidian 界面
obsidian eval "console.log('hello')"          # 在 Obsidian 环境执行 JS
```

**如何触发：**
```
"在 Obsidian 里创建一个关于 [主题] 的笔记"
"把今天的工作记录追加到日记里"
"搜索我 Obsidian 里关于 Claude Code 的笔记"
"查看这个笔记的所有反向链接"
```

---

### caveman
**来源：** `JuliusBrussee/caveman`

**用途：** **通信压缩模式**，不是"用最简单方案"——而是让 Claude 的回复更短、更直接，减少 ~75% 无效 token。技术内容完全保留，只删冗余。

**什么时候用：**
- 感觉 Claude 回复太长、废话太多
- 想节省 token 消耗
- 快节奏调试/迭代阶段，需要简短响应
- 不需要解释、只要结果

**6 种强度：**

| 强度 | 命令 | 效果 |
|------|------|------|
| lite | `/caveman lite` | 轻度压缩，去掉客套话 |
| full（默认） | `/caveman` 或 `/caveman full` | 大幅压缩，片段式回复 |
| ultra | `/caveman ultra` | 极端压缩，仅保留核心信息 |
| wenyan-lite | `/caveman wenyan-lite` | 文言文轻度 |
| wenyan-full | `/caveman wenyan-full` | 文言文完整 |
| wenyan-ultra | `/caveman wenyan-ultra` | 极简文言文 |

**如何触发：**
```
/caveman                       ← 启动 full 模式
/caveman lite                  ← 轻度压缩
/caveman ultra                 ← 极端压缩
"stop caveman" / "normal mode" ← 关闭
```

**自动恢复正常：** 安全警告、不可逆操作确认、多步序列（顺序关键时）——这些场景 caveman 自动暂停，说完再恢复。

**不受 caveman 影响：** 代码块、commit message、PR 描述——这些始终正常书写。

---

### planning-with-files
**来源：** `OthmanAdi/planning-with-files` | **版本：** 2.40.0

**用途：** Manus 风格的文件化任务规划，将计划、发现和进度写入 3 个持久化 Markdown 文件，会话中断后仍可恢复。

**3 个核心文件：**

| 文件 | 用途 |
|------|------|
| `task_plan.md` | 任务分解、阶段、依赖关系 |
| `findings.md` | 研究发现、代码分析结果 |
| `progress.md` | 已完成步骤、当前状态、下一步 |

**关键约束：**
- **2-action 规则**：每轮最多执行 2 个 tool call，然后必须更新文件
- **3-strike 协议**：同一错误出现 3 次，停止执行，汇报问题请求指示
- **自动上下文恢复**：每次 prompt hook 触发时读取 3 个文件，无需手动交代背景

**如何触发：**
```
/plan-goal 实现用户认证模块    ← 启动规划模式，创建 3 个文件
/plan-loop                      ← 继续执行循环（读文件 → 行动 → 更新文件）

"把这个任务的计划写进文件"
"创建一个实现计划并保存"
"按照计划文件继续执行下一步"
```

**会话中断恢复：**
```
"继续上次的任务"  → Claude 读取 progress.md 自动恢复上下文
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
**来源：** `jarrodwatts/claude-hud` | **版本：** 0.1.0

**用途：** 在 statusline 实时显示 4 行状态信息：模型/context 用量、工具活动、Agent 状态、Todo 进度。

**4 行显示内容：**
```
行1（始终显示）：[Sonnet] █████░░░░░ 45% | project git:(main) | 2 CLAUDE.md | 5h: 25% | ⏱ 5m
行2（有工具时）：◐ Edit: auth.ts | ✓ Read ×3 | ✓ Grep ×2
行3（有 Agent 时）：◐ explore [haiku]: Finding auth code (2m 15s)
行4（有 Todo 时）：▸ Fix authentication bug (2/5)
```

**Context 颜色阈值：**
- 绿色 < 70%：健康
- 黄色 70-85%：注意
- 红色 > 85%：危险（显示 token 详细分解）

**什么时候用：**
- 长会话中监控剩余 context 空间
- 监控 token 成本和速率限制用量
- 调试多 Agent 并行任务时追踪状态

**如何触发：** 安装后需运行 `/claude-hud:setup` 配置 statusline，然后重启 Claude Code 自动显示。无需每次手动调用。

---

### codex
**来源：** `openai/codex-plugin-cc`

**用途：** 将任务**转发给 Codex CLI** 执行，通过 `node codex-companion.mjs task "<任务描述>"` 单次调用。本质是 rescue 转发器，不在 Claude 侧分析或执行任务本身。

**什么时候用：**
- 显式委派某个任务给 Codex 处理（"把这个交给 Codex"）
- 需要 Codex 的写入能力对 repo 做修改
- 上次 Codex 任务未完成，需要继续（`--resume`）

**如何触发：**
```
"用 Codex 执行这个任务：[描述]"
"把这个 bug 修复委派给 Codex"
"Codex 继续上次的任务"           ← 触发 --resume-last
"Codex fresh 重新开始"           ← 触发新任务不 resume
```

**注意：**
- codex:codex-rescue 只调用 `task` 一次，返回原始输出，不做二次分析
- 默认加 `--write`（写入模式）；想只读分析时需明确说"只读"
- Claude 侧不读文件、不分析代码、不做独立判断

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

**用途：** 通过 MCP 工具或 REST API 在本地 Excalidraw 画布上创建和编辑图表，支持实时同步、迭代修正和导出。

**前提：** 需要运行画布服务器：
```bash
git clone https://github.com/yctimlin/mcp_excalidraw && cd mcp_excalidraw
npm ci && npm run build
PORT=3000 npm run canvas   # 然后浏览器打开 http://127.0.0.1:3000
```

**两种工作模式（MCP 优先）：**
- **MCP 模式**（推荐）：工具列表中有 `excalidraw/*` 工具时直接使用
- **REST API 模式**（回退）：调用 `http://127.0.0.1:3000` 端点

**核心工作流：**
```
1. 规划坐标布局（x 右增，y 下增）
2. batch_create_elements 批量创建元素 + 箭头
3. get_canvas_screenshot 截图检查
4. 发现问题（文字截断/重叠/箭头穿越）→ update_element 修复
5. 确认无问题后继续下一批元素
```

**关键陷阱：**
- 背景区域 rectangle 不要加 `text`/`label`，改用独立 text 元素放顶角
- 跨区域箭头会形成 spaghetti，用 elbowed 路由或注解代替
- 箭头标签 ≤ 12 字符，稀疏使用

**如何触发：**
```
"画一个三层架构图：前端、后端、数据库"
"把这个用户注册流程画成流程图"
"把这段 Mermaid 转成 Excalidraw"
```

---

### notebooklm
**文件：** `notebooklm.md` | **来源：** `PleasePrompto/notebooklm-skill`
**调用：** `/notebooklm`

**用途：** 通过浏览器自动化操控 NotebookLM，上传内容、提问、导出摘要。

**关键规则：**
- **始终用 `python scripts/run.py <脚本路径>`** 执行脚本（绝不直接调用 Python 脚本）
- 每次开始前先检查 auth 状态
- 操作完成后自动进入追问循环，直到用户满意为止

**工作流程：**
```bash
# 1. 检查登录状态
python scripts/run.py scripts/check_auth.py

# 2. 上传内容并提问
python scripts/run.py scripts/upload_and_query.py --content "内容路径" --question "问题"

# 3. 导出结果
python scripts/run.py scripts/export_summary.py
```

**如何触发：**
```
/notebooklm
"把这篇研究报告上传到 NotebookLM 并生成摘要"
"用 NotebookLM 分析这几个文档的共同主题"
```

---

### humanizer
**文件：** `humanizer.md` | **来源：** `blader/humanizer`
**调用：** `/humanizer`

**用途：** 识别并消除 AI 写作痕迹，让文字读起来像真人写的。基于 Wikipedia "Signs of AI writing" 指南。

**识别的 AI 写作模式（部分）：**
- **词汇**：overuse of "delve/leverage/elevate/foster/pivotal/robust/ensure"
- **标点**：em dash 过度使用（"this — that"）
- **结构**：三点并列（rule of three）、被动语态、负面并列
- **语气**：推广语言（"comprehensive/transformative/revolutionize"）、模糊归因（"studies show"）
- **分析**：表面的 -ing 分析（"by doing X, we achieve Y"）
- **填充**：空洞短语（"it's worth noting that"、"in today's world"）

**两步流程：**
1. **扫描 + 改写**：找出 AI 模式，用自然替代方案重写
2. **反 AI 终审**：提问"这段文字最明显的 AI 特征是什么？"→ 答题 → 再修一遍

**声音匹配（可选）：**
```
"Humanize 这段文字。这是我的写作样本用于声音匹配：[样本]"
"用 [文件路径] 里的风格来改写这段文字"
```
匹配维度：句长模式、用词层次、段落开头习惯、标点习惯。

**如何触发：**
```
/humanizer
"把这段产品介绍改写得更自然"
"这封邮件太正式了，帮我改得口语化一点"
"这篇报告的总结 AI 味太重，humanize 一下"
```

---

### pptx
**文件：** `pptx.md` | **来源：** `anthropics/skills`
**调用：** `/pptx`（或提到 .pptx/deck/slides/presentation 关键词时自动触发）

**用途：** 创建、编辑和分析 `.pptx` PowerPoint 文件。

**自动触发条件：** 用户提到 "deck"/"slides"/"presentation"/".pptx 文件名" 时自动激活。

**三种操作模式：**

| 场景 | 工具 | 命令 |
|------|------|------|
| 读取/分析内容 | markitdown | `python -m markitdown presentation.pptx` |
| 从模板编辑 | 解包→修改→打包 | `unpack.py` → 编辑 XML → 重打包 |
| 从零创建 | pptxgenjs | `npm install -g pptxgenjs` |

**视觉检查工作流（必做）：**
```bash
# 转换为图片
python scripts/office/soffice.py --headless --convert-to pdf output.pptx
pdftoppm -jpeg -r 150 output.pdf slide
# 生成 slide-01.jpg, slide-02.jpg...

# 用 subagent 视觉检查（必须用 subagent，自己查容易漏）
# 检查：文字溢出、元素重叠、间距不均、占位符未替换、低对比度
```

**设计原则（避免 AI Slop PPT）：**
- 每张幻灯片必须有视觉元素（图、图表、图标、形状）
- 大标题 36-44pt，正文 14-16pt，不要用 Arial
- 颜色方案不要默认蓝色，选与主题匹配的
- **绝不在标题下加装饰线**（AI 生成 PPT 的典型特征）

---

### code-review-graph
**文件：** `crg-*.md`（7 个）| **来源：** `tirth8205/code-review-graph`

**用途：** 基于**代码依赖知识图谱**的审查和重构工具集，从图结构角度理解变更的爆炸半径（blast radius）。

**核心知识图谱工具（按需自动调用）：**
```
build_or_update_graph_tool(base="main")              # 构建/更新依赖图
get_review_context_tool(base="main")                 # 获取 PR 所有变更文件
get_impact_radius_tool(base="main")                  # 分析爆炸半径
query_graph_tool(pattern="callers_of", target=<func>) # 查调用者
query_graph_tool(pattern="tests_for", target=<func>)  # 查测试覆盖
semantic_search_nodes_tool(...)                       # 语义搜索相关节点
```

**7 个子命令：**

| 命令 | 用途 | 用法示例 |
|------|------|---------|
| `/crg-build-graph` | 构建代码依赖图，可视化模块关系 | "为这个项目构建依赖图" |
| `/crg-explore-codebase` | 图式探索代码库，快速理解陌生项目 | "帮我了解这个项目的结构" |
| `/crg-review-pr` | PR 级别全面代码审查，输出结构化报告 | "审查这个 PR 的所有改动" |
| `/crg-review-changes` | 针对当前变更的审查 | "检查我刚写的这些改动" |
| `/crg-review-delta` | 增量差异对比审查 | "对比上个版本，分析新增的问题" |
| `/crg-debug-issue` | 利用依赖图定位 bug 根源 | "这个 bug 影响了哪些模块" |
| `/crg-refactor-safely` | 分析影响范围，给出安全重构建议 | "我想重构这个模块，有哪些风险" |

**`/crg-review-pr` 输出格式：**
```
## PR Review: <标题>

### Risk Assessment
- Overall risk: Low / Medium / High
- Blast radius: X files, Y functions impacted
- Test coverage: N changed functions covered / M total

### File-by-File Review
#### <file_path>
- Changes: <描述>
- Impact: <依赖此文件的模块>
- Issues: <bug/规范/隐患>

### Missing Tests / Recommendations
```

---


### multi-stage-dockerfile
**文件：** `multi-stage-dockerfile.md` | **来源：** `github/awesome-copilot`
**调用：** `/multi-stage-dockerfile`

**用途：** 为任意语言或框架生成优化的多阶段 Dockerfile，减小镜像体积、提升构建缓存效率。

**什么时候用：**
- 需要为项目写 Dockerfile 且不熟悉最佳实践
- 想优化现有 Dockerfile（镜像太大、构建慢）
- 多阶段构建分离 build 和 runtime 环境

**如何触发：**
```
/multi-stage-dockerfile
"给这个 Node.js 项目写一个多阶段 Dockerfile"
"优化一下这个 Dockerfile，镜像太大了"
"给 Python FastAPI 应用生成 Dockerfile"
```

---

### shadcn
**文件：** `shadcn.md` | **来源：** `shadcn/ui`（官方）
**调用：** 自动触发（`user-invocable: false`）

**用途：** 管理 shadcn/ui 组件——添加、搜索、调试、样式定制、组合 UI。自动读取项目 config 和已安装组件列表。

**自动触发条件：**
- 项目存在 `components.json`
- 提到 "shadcn"、"shadcn/ui"、组件名（Button/Card/Dialog 等）
- 执行 `shadcn init` 或 `--preset` 相关操作

**常用操作：**
```bash
# 添加组件（自动用项目的包管理器）
npx shadcn@latest add button
npx shadcn@latest add card dialog form

# 查看组件文档
npx shadcn@latest docs button

# 初始化项目
npx shadcn@latest init

# 查看当前项目配置和已安装组件
npx shadcn@latest info --json
```

**如何触发：**
```
"给这个页面加一个 shadcn 的 Dialog 组件"
"用 shadcn Card 实现这个布局"
"初始化 shadcn/ui 到这个项目"
```

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
