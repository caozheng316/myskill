---
name: skill-sync
description: This skill should be used when the user asks to "同步skill", "推送skill", "拉取skill", "备份skill", "同步技能", "skill同步", "skill推送", "skill拉取", mentions "同步skill", "skill同步", "skill到git", "skill到github", or wants to sync Claude Code skills to/from GitHub.
version: 1.0.0
---

# Role: Skill 同步管理专家

## 核心目标

帮助用户将 Claude Code 的自定义 skill 同步到 GitHub，实现多设备间的技能共享和备份。

## 配置信息

- **本地 Skill 目录**: `~/.claude/skills/`
- **远程仓库**: `https://github.com/caozheng316/myskill.git`
- **默认分支**: `main`

## 工作流程

### 场景一：推送本地 Skill 到 GitHub

当用户想要同步本地 skill 到 GitHub 时：

1. **检查当前状态**
   ```bash
   cd ~/.claude/skills
   git status
   ```

2. **添加并提交变更**
   ```bash
   cd ~/.claude/skills
   git add .
   git commit -m "更新技能: <描述变更内容>"
   ```

3. **推送到远程**
   ```bash
   cd ~/.claude/skills
   git push
   ```

### 场景二：从 GitHub 拉取 Skill

当用户想在当前设备同步远程 skill 时：

```bash
cd ~/.claude/skills
git pull
```

### 场景三：新设备首次克隆

当用户在新设备上首次使用时：

```bash
# 备份现有 skills（如果有）
mv ~/.claude/skills ~/.claude/skills.bak 2>/dev/null

# 克隆仓库
git clone https://github.com/caozheng316/myskill.git ~/.claude/skills
```

### 场景四：查看同步状态

查看当前 skill 目录的 git 状态和最近的提交：

```bash
cd ~/.claude/skills
git status
git log --oneline -5
```

## 常用命令速查

### 推送更新
```bash
cd ~/.claude/skills && git add . && git commit -m "更新技能" && git push
```

### 拉取更新
```bash
cd ~/.claude/skills && git pull
```

### 查看差异
```bash
cd ~/.claude/skills && git diff
cd ~/.claude/skills && git log --oneline -10
```

### 强制同步到远程（覆盖远程）
```bash
cd ~/.claude/skills && git add . && git commit -m "强制同步" && git push -f
```

### 放弃本地更改（同步到远程状态）
```bash
cd ~/.claude/skills && git fetch origin && git reset --hard origin/main
```

## 处理冲突

如果拉取时出现冲突：

1. 查看冲突文件：`git status`
2. 手动解决冲突后：
   ```bash
   git add .
   git commit -m "解决冲突"
   git push
   ```

## 启动行为

当此 skill 被激活时，简要说明：
1. 可以帮用户同步 skill 到 GitHub (caozheng316/myskill)
2. 询问用户想要执行什么操作（推送/拉取/查看状态/新设备克隆）
