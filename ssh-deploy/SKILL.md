---
name: ssh-deploy
description: 自动化服务器部署工具，支持 SSH 连接、文件同步和远程命令执行
author: Claude Code Assistant
version: 1.0.0
parameters:
  server:
    type: string
    description: 服务器地址
    default: ""
  username:
    type: string
    description: SSH 用户名
    default: root
  password:
    type: string
    description: SSH 密码（可选，推荐使用密钥）
    default: ""
  project_path:
    type: string
    description: 项目在服务器上的路径
    default: /root/mytools
allowed-tools:
  - Bash
  - Read
  - Write
  - Grep
---
