# install-from-github

Claude Code skill — 从 GitHub 仓库安装 skills 到本地 `~/.claude/skills/`。

## 安装

```bash
git clone https://github.com/Camusugar/install-from-github.git ~/.claude/skills/install-from-github
```

## 使用

```
/install-from-github <github仓库链接>
```

例如：

```
/install-from-github https://github.com/anthropics/skills
```

支持指定分支：

```
/install-from-github https://github.com/anthropics/skills --branch dev
```

## 工作流程

1. 克隆目标仓库，扫描所有 `SKILL.md` 文件
2. 以复选框让用户选择要安装哪些 skill（已安装的标记 ⚠）
3. 一键安装选中 skills 并清理临时文件

## 许可

MIT
