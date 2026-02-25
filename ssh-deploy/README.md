# SSH 部署技能

## 功能

自动化的服务器部署工具，支持：
- SSH 密钥和密码认证
- 文件同步（SCP）
- 远程命令执行
- Docker 服务管理

## 使用方法

### 1. 配置服务器信息

首先在 `~/.ssh/config` 中添加服务器配置：

```
Host myserver
    HostName YOUR_SERVER_IP
    User root
    IdentityFile ~/.ssh/id_ed25519
    StrictHostKeyChecking no
```

### 2. 设置 SSH 密钥（推荐）

```bash
# 生成密钥
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519

# 复制公钥到服务器
ssh-copy-id -i ~/.ssh/id_ed25519 root@YOUR_SERVER_IP
```

### 3. 使用部署脚本

```bash
# Python 部署脚本
python /path/to/mytools/deploy.py

# 或手动执行
ssh myserver "cd /root/mytools && git pull && docker-compose up -d"
```

## 技能特性

- ✅ 自动密钥认证
- ✅ 密码回退机制
- ✅ 增量文件同步
- ✅ 远程命令执行
- ✅ 实时日志输出
