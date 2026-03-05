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

### 一键安装（推荐）

```bash
# 安装面试官模拟 Skill
npx skills add https://github.com/codedogQBY/interviewer-skills/tree/main/bytedance-frontend-interviewer --skill bytedance-frontend-interviewer

# 安装面试应答 Skill
npx skills add https://github.com/codedogQBY/interviewer-skills/tree/main/frontend-interview-responder --skill frontend-interview-responder
```

支持所有主流 AI 编程工具：**Cursor** / **Claude Code** / **Windsurf** / **CodeBuddy** / **Trae** / **GitHub Copilot** / **Cline** / **Codex CLI** / **Roo Code** / **Aider** 等。

### 手动安装

将 skill 目录（含 `SKILL.md`）复制到对应工具的 Skills 加载路径：

| 工具 | 全局路径 | 项目级路径 |
|------|---------|-----------|
| **Claude Code** | `~/.claude/skills/` | `.claude/skills/` |
| **Cursor** | `~/.cursor/skills/` | `.cursor/skills/` |
| **Windsurf** | `~/.codeium/windsurf/skills/` | `.windsurf/skills/` |
| **CodeBuddy** | 用户级 Skill 目录 | 项目根目录 |
| **Trae** | `~/.trae/skills/` | `.trae/skills/` |
| **GitHub Copilot** | 个人级 Skill 目录 | `.github/skills/` |
| **Cline** | `~/.cline/skills/` | `.agents/skills/` |
| **Codex CLI** | `~/.codex/skills/` | `.agents/skills/` |
| **Roo Code** | `~/.roo/skills/` | `.roo/skills/` |
| **Augment Code** | 通过 IDE 设置导入 | 项目级自定义指令 |
| **Aider** | `~/.aider/skills/` | `.aider/skills/` |

### 直接粘贴到对话

将 `SKILL.md` 内容直接粘贴到 AI 对话中即可，适用于 **JetBrains AI** / **Amazon Q** / **Gemini Code Assist** / **通义灵码** 等任何支持自定义 Prompt 的工具。

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
