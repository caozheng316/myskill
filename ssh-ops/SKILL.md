---
name: ssh-ops
description: This skill should be used when the user asks to "SSH操作", "远程服务器", "连接服务器", "执行远程命令", "服务器管理", "查看服务器状态", "服务器运维", "SSH到服务器", "远程执行", mentions "SSH", "远程服务器", "服务器操作", "myserver", or wants to execute commands on remote servers.
version: 1.1.0
---

# Role: SSH 远程服务器操作专家

## 核心目标

帮助用户安全地通过 SSH 执行远程服务器操作，包括命令执行、文件传输、服务管理等。

## ⚠️ 最重要的规则：执行前必须询问

**在执行任何 SSH 命令之前，必须先询问用户是否允许执行！**

询问格式：
```
我需要在服务器上执行以下命令：
  ssh root@39.96.204.4 "<command>"

是否帮你执行？[Y/n]
```

只有用户明确同意后（回复 Y、yes、是 等），才能执行 SSH 命令。

## 服务器信息

- **地址**: 39.96.204.4
- **用户**: root
- **云服务商**: 阿里云 ECS
- **系统**: Ubuntu
- **认证方式**: SSH 密钥认证（已配置，免密登录）
- **已安装**: Docker, tmux

## 项目信息

- **项目目录**: ~/mytools
- **GitHub**: https://github.com/caozheng316/mytools.git
- **服务端口**: 80 (HTTP)

## 工作流程

### 第一步：理解需求

当用户请求服务器操作时，明确：
1. 要执行什么操作？
2. 是否涉及敏感操作（删除、重启、修改配置）？
3. 需要查看还是修改？

### 第二步：确认命令

根据需求构造 SSH 命令，并向用户展示将要执行的完整命令。

**注意**：
- 使用 `ssh root@39.96.204.4` 而不是 `ssh myserver`（别名未配置）
- SSH 密钥已配置，无需输入密码

### 第三步：请求授权

询问用户是否允许执行：
```
我需要在服务器上执行以下命令：
  ssh root@39.96.204.4 "docker logs my-tools-backend --tail 50"

是否帮你执行？[Y/n]
```

### 第四步：执行并反馈

用户确认后执行命令，返回结果。

## 常用命令速查

### 系统信息
```bash
ssh root@39.96.204.4 "hostname && uptime"
ssh root@39.96.204.4 "free -h"              # 内存
ssh root@39.96.204.4 "df -h"                # 磁盘
ssh root@39.96.204.4 "cat /etc/os-release"  # 系统版本
```

### Docker 操作（mytools 项目）
```bash
# 进入项目目录
ssh root@39.96.204.4 "cd ~/mytools && docker-compose ps"

# 查看日志
ssh root@39.96.204.4 "cd ~/mytools && docker-compose logs --tail=50 backend"

# 重启服务
ssh root@39.96.204.4 "cd ~/mytools && docker-compose restart"

# 重新构建并启动
ssh root@39.96.204.4 "cd ~/mytools && docker-compose down && docker-compose up -d --build"
```

### 数据库操作
```bash
# 进入容器执行 Python
ssh root@39.96.204.4 "docker exec my-tools-backend python -c '<code>'"

# 查看数据库
ssh root@39.96.204.4 "docker exec my-tools-backend python -c \"
import asyncio
from app.database import async_session_maker
from app.models import Document
from sqlalchemy import select

async def check():
    async with async_session_maker() as db:
        result = await db.execute(select(Document))
        print(f'Documents: {len(result.scalars().all())}')

asyncio.run(check())
\"""
```

### 文件传输
```bash
# 上传单个文件
scp -o StrictHostKeyChecking=no local_file root@39.96.204.4:/root/mytools/path/

# 上传多个文件
scp -o StrictHostKeyChecking=no file1 file2 root@39.96.204.4:/root/mytools/path/

# 下载文件
scp root@39.96.204.4:/root/mytools/path/file ./
```

### Git 操作
```bash
# 拉取最新代码（注意：服务器网络可能不稳定）
ssh root@39.96.204.4 "cd ~/mytools && git pull"

# 如果 git pull 失败，可使用 scp 传输文件后重新构建
```

## 常见操作场景

### 部署流程
```bash
# 1. 上传修改后的文件
scp -o StrictHostKeyChecking.no backend/app/xxx/router.py root@39.96.204.4:/root/mytools/backend/app/xxx/

# 2. 重新构建并启动
ssh root@39.96.204.4 "cd ~/mytools && docker-compose stop backend && docker-compose rm -f backend && docker-compose build backend && docker-compose up -d"

# 3. 验证服务状态
ssh root@39.96.204.4 "cd ~/mytools && docker-compose ps"
```

### 清空数据
```bash
# 清空所有文档和任务
ssh root@39.96.204.4 "docker exec my-tools-backend python -c \"
import asyncio
from app.database import async_session_maker
from app.models import Document, Task
from sqlalchemy import select
from sqlalchemy.orm import selectinload

async def delete_all():
    async with async_session_maker() as db:
        result = await db.execute(select(Document).options(selectinload(Document.tasks)))
        docs = result.scalars().all()
        for doc in docs:
            for task in doc.tasks:
                await db.delete(task)
            await db.delete(doc)
        await db.commit()
        print('Deleted {} documents'.format(len(docs)))

asyncio.run(delete_all())
\"""
```

### 查看日志
```bash
# 查看后端日志
ssh root@39.96.204.4 "cd ~/mytools && docker-compose logs --tail=100 backend | grep -i error"

# 实时跟踪日志
ssh root@39.96.204.4 "cd ~/mytools && docker-compose logs -f backend"
```

## 环境说明

### 本地环境
- **操作系统**: Windows 10
- **Shell**: Git Bash (/usr/bin/bash)
- **项目路径**: ~/Desktop/mytools

### 工具可用性
- ✅ ssh: 可用
- ✅ scp: 可用
- ❌ sshpass: 不可用（不需要，已配置密钥）
- ❌ expect: 不可用
- ❌ myserver 别名: 未配置

### 注意事项

1. **SSH 密钥已配置**：可以直接使用 `ssh root@39.96.204.4` 免密登录
2. **Git Bash 环境**：使用 Unix shell 语法，路径用 `/` 而不是 `\\`
3. **服务器网络**：连接 GitHub 可能不稳定，建议使用 scp 传输文件
4. **敏感操作**：删除、重启、清空数据等要特别提醒用户
5. **管道符转义**：在 bash 中 `|` 不需要转义，但 `$` 符号可能需要转义

## 故障排查

### SSH 连接问题
```bash
# 测试连接
ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10 root@39.96.204.4 "echo OK"

# 查看密钥
ls -la ~/.ssh/
cat ~/.ssh/id_ed25519.pub
```

### Docker 问题
```bash
# 查看容器状态
ssh root@39.96.204.4 "docker ps -a"

# 查看容器日志
ssh root@39.96.204.4 "docker logs my-tools-backend --tail 100"

# 重启容器
ssh root@39.96.204.4 "docker restart my-tools-backend"
```

### 网络问题
```bash
# 测试服务可访问性
ssh root@39.96.204.4 "curl -I http://localhost"

# 查看端口占用
ssh root@39.96.204.4 "netstat -tlnp | grep :80"
```

## 启动行为

当此 skill 被激活时，简要说明：
1. 可以帮用户操作远程服务器 (39.96.204.4)
2. SSH 密钥已配置，可以免密登录
3. 询问用户想要执行什么操作
4. 提醒用户所有命令执行前都会先确认
