# Interviewer Skills

一组面试相关的 AI Agent Skills 集合，帮助前端开发者备战技术面试。适用于主流 AI 编程工具（均已支持 Skill 机制）。

## Skills 列表

| Skill | 说明 | 触发关键词 |
|-------|------|-----------|
| [bytedance-frontend-interviewer](./bytedance-frontend-interviewer/) | 字节 2-1 前端面试官模拟，深挖项目 + 辅导回答 + 10 分制打分 | 模拟面试、前端面试、字节面试、项目深挖、面试准备、挖掘亮点 |
| [frontend-interview-responder](./frontend-interview-responder/) | 前端面试应答助手，扫描项目后以候选人身份简洁作答 | 面试回答、模拟回答、帮我回答、怎么回答面试官、面试应答 |

## 目录结构

```
interviewer-skills/
├── bytedance-frontend-interviewer/   # 面试官模拟 & 项目深挖教练
│   ├── SKILL.md
│   └── references/
│       └── interview-knowledge.md
├── frontend-interview-responder/     # 面试应答助手
│   ├── SKILL.md
│   └── references/
│       └── answer-guide.md
└── README.md
```

## 如何使用

主流 AI 编程工具均已支持 Agent Skills 机制（基于 `SKILL.md` 标准格式）。以下是各工具的安装方式。

### 方式一：通过 CLI 工具一键安装

```bash
# OpenSkills（通用，支持 Cursor / Claude Code / Windsurf / CodeBuddy 等）
npm i -g openskills
openskills install <github-repo-or-skill-name>

# ClawHub CLI（支持 OpenClaw 生态工具）
npx clawhub@latest install <skill-name>
```

### 方式二：手动安装到各工具 Skills 目录

将 skill 目录（含 `SKILL.md`）复制到对应路径即可：

| 工具 | 全局路径 | 项目级路径 |
|------|---------|-----------|
| **Claude Code** | `~/.claude/skills/` | `<项目>/.claude/skills/` |
| **Cursor** | `~/.cursor/skills/` | `<项目>/.cursor/skills/` |
| **Windsurf** | `~/.codeium/windsurf/skills/` | `<项目>/.windsurf/skills/` |
| **CodeBuddy** | 用户级 Skill 目录 | 项目根目录 |
| **Trae（字节）** | `~/.trae/skills/` | `<项目>/.trae/skills/` |
| **GitHub Copilot** | 个人级 Skill 目录 | `<项目>/.github/skills/` |
| **Cline** | `~/.cline/skills/` | `<项目>/.agents/skills/` |
| **OpenAI Codex CLI** | `~/.codex/skills/` | `<项目>/.agents/skills/` |
| **Roo Code** | `~/.roo/skills/` | `<项目>/.roo/skills/` |
| **Augment Code** | 通过 IDE 设置导入 | 项目级自定义指令 |
| **Aider** | `~/.aider/skills/` | `<项目>/.aider/skills/` |

> 路径优先级：项目级 > 全局。部分工具的路径可能随版本更新变化，请参考各工具官方文档。

### 方式三：通过工具内 UI 导入

以下工具支持在设置界面中直接导入 `SKILL.md` 文件：

- **Trae**：设置 → 规则和技能 → 技能 → 创建 → 导入文件
- **Cursor**：Settings → Rules → Skills → 导入
- **CodeBuddy**：对话中使用触发关键词自动激活，或通过 Skill 管理面板添加

### 方式四：通过 Claude Code 插件命令安装

```bash
# 在 Claude Code 终端中执行
/plugin marketplace add anthropics/skills
/plugin install <skill-package-name>

# 或直接使用 /skill 命令管理
/skill list
/skill add <path-to-skill-directory>
```

### 方式五：直接粘贴到对话

将 `SKILL.md` 的内容直接粘贴到任何 AI 对话中即可使用，适用于：

- **JetBrains AI Assistant** — 粘贴到聊天窗口或自定义 Prompt
- **Amazon Q Developer** — 粘贴到对话上下文
- **Gemini Code Assist** — 粘贴到聊天界面
- **通义灵码（Lingma）** — 粘贴到对话窗口
- 以及任何支持自定义 System Prompt 的工具

### 兼容性说明

所有 Skills 遵循 [Agent Skills 开放规范](https://github.com/agentskills/agentskills)，核心是 `SKILL.md` + YAML frontmatter 的标准结构，可在支持该规范的工具间无缝迁移。

## 如何新增 Skill

每个 skill 是一个独立目录，基本结构：

```
your-skill-name/
├── SKILL.md              # skill 定义（必须）
├── references/           # 参考知识库（可选）
├── scripts/              # 辅助脚本（可选）
└── assets/               # 静态资源（可选）
```

`SKILL.md` 需包含 frontmatter：

```yaml
---
name: your-skill-name
description: "Skill 描述，包含触发关键词..."
---
```

## 规划中

- 算法面试辅导
- 行为面试（BQ）模拟
- 简历优化助手
- 系统设计面试模拟
