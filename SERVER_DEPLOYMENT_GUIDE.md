# Windows 服务器部署指南

本指南详细说明如何在 Windows 服务器上从 GitHub 克隆代码并部署 Node.js 后端服务。

## 📋 前置要求

### 1. 安装 Git for Windows

1. 访问 [Git 官网](https://git-scm.com/download/win) 下载安装包
2. 运行安装程序,使用默认选项即可
3. 验证安装:
   ```powershell
   git --version
   ```

### 2. 安装 Node.js

1. 访问 [Node.js 官网](https://nodejs.org/)
2. 下载 LTS 版本(推荐 18.x 或更高)
3. 运行安装程序,确保勾选 "Add to PATH"
4. 验证安装:
   ```powershell
   node -v
   npm -v
   ```

### 3. 配置 SSH 密钥(推荐)

如果使用 SSH 方式克隆仓库:

```powershell
# 生成 SSH 密钥
ssh-keygen -t ed25519 -C "your_email@example.com"

# 查看公钥
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub

# 将公钥添加到 GitHub
# 访问 https://github.com/settings/keys
```

---

## 🚀 部署步骤

### 第一步:克隆代码

```powershell
# 进入工作目录(例如 C:\Projects)
cd C:\Projects

# 克隆仓库(SSH 方式)
git clone git@github.com:pleasureswx123/chat.git

# 或使用 HTTPS 方式
git clone https://github.com/pleasureswx123/chat.git

# 进入项目目录
cd chat\coze_oauth_nodejs_jwt
```

### 第二步:配置应用

```powershell
# 1. 复制配置文件模板
Copy-Item coze_oauth_config.json.example coze_oauth_config.json

# 2. 编辑配置文件
notepad coze_oauth_config.json
```

配置文件内容示例:
```json
{
  "client_type": "jwt",
  "client_id": "你的客户端ID",
  "public_key_id": "你的公钥ID",
  "private_key": "-----BEGIN PRIVATE KEY-----\n你的私钥内容\n-----END PRIVATE KEY-----",
  "coze_www_base": "https://www.coze.cn",
  "coze_api_base": "https://api.coze.cn"
}
```

### 第三步:部署服务

#### 方式一:使用 PM2(推荐)

```powershell
# 运行一键部署脚本
.\deploy.ps1
```

部署脚本会自动:
- ✅ 检查 Node.js 环境
- ✅ 安装 PM2
- ✅ 检查配置文件
- ✅ 创建日志目录
- ✅ 安装项目依赖
- ✅ 启动服务

#### 方式二:安装为 Windows 服务(开机自启)

```powershell
# 1. 以管理员身份运行 PowerShell
# 右键点击 PowerShell 图标 → "以管理员身份运行"

# 2. 进入项目目录
cd C:\Projects\chat\coze_oauth_nodejs_jwt

# 3. 安装 Windows 服务
.\install-service.ps1

# 4. 部署应用
.\deploy.ps1

# 5. 保存 PM2 进程列表
pm2 save
```

### 第四步:验证部署

```powershell
# 查看服务状态
pm2 status

# 查看日志
pm2 logs coze-oauth-service

# 健康检查
.\health-check.ps1

# 访问服务
# 打开浏览器访问: http://localhost:8080
```

---

## 🔧 配置防火墙

如果需要外网访问,需要开放端口:

```powershell
# 以管理员身份运行
New-NetFirewallRule -DisplayName "Coze OAuth Service" `
  -Direction Inbound `
  -LocalPort 8080 `
  -Protocol TCP `
  -Action Allow
```

---

## 📊 服务管理

### PM2 常用命令

```powershell
# 查看所有服务
pm2 list

# 查看日志
pm2 logs coze-oauth-service

# 重启服务
pm2 restart coze-oauth-service

# 停止服务
pm2 stop coze-oauth-service

# 删除服务
pm2 delete coze-oauth-service
```

### Windows 服务管理

```powershell
# 启动服务
Start-Service PM2

# 停止服务
Stop-Service PM2

# 重启服务
Restart-Service PM2

# 查看状态
Get-Service PM2
```

---

## 🔄 更新部署

当代码有更新时:

```powershell
# 1. 进入项目目录
cd C:\Projects\chat\coze_oauth_nodejs_jwt

# 2. 备份当前配置
.\backup.ps1

# 3. 拉取最新代码
git pull

# 4. 安装依赖
npm install --production

# 5. 重启服务
pm2 restart coze-oauth-service

# 6. 查看日志确认
pm2 logs coze-oauth-service --lines 50
```

---

## 🛠️ 故障排查

### 问题 1: Git 克隆失败

**错误**: `Permission denied (publickey)`

**解决方案**:
1. 检查 SSH 密钥是否已添加到 GitHub
2. 或使用 HTTPS 方式克隆

### 问题 2: PM2 命令找不到

**错误**: `pm2 : 无法将"pm2"项识别为 cmdlet`

**解决方案**:
```powershell
# 查看 npm 全局目录
npm config get prefix

# 添加到 PATH(以管理员身份运行)
$npmPath = npm config get prefix
[Environment]::SetEnvironmentVariable("Path", $env:Path + ";$npmPath", "Machine")

# 重启 PowerShell
```

### 问题 3: 端口被占用

**错误**: `Error: listen EADDRINUSE: address already in use :::8080`

**解决方案**:
```powershell
# 查看占用端口的进程
netstat -ano | findstr :8080

# 结束进程
taskkill /PID <进程ID> /F
```

### 问题 4: 服务无法启动

**解决方案**:
```powershell
# 1. 查看错误日志
pm2 logs coze-oauth-service --err

# 2. 检查配置文件
Get-Content coze_oauth_config.json

# 3. 检查 Node.js 版本
node -v  # 应该 >= 14

# 4. 重新部署
pm2 delete coze-oauth-service
.\deploy.ps1
```

---

## 📁 目录结构

```
C:\Projects\chat\
├── coze_oauth_nodejs_jwt\          # 后端服务目录
│   ├── index.js                    # 服务入口文件
│   ├── package.json                # 依赖配置
│   ├── ecosystem.config.js         # PM2 配置
│   ├── coze_oauth_config.json      # 应用配置(需手动创建)
│   ├── deploy.ps1                  # 部署脚本
│   ├── install-service.ps1         # 服务安装脚本
│   ├── health-check.ps1            # 健康检查脚本
│   ├── backup.ps1                  # 备份脚本
│   ├── logs\                       # 日志目录
│   ├── websites\                   # 静态资源
│   └── DEPLOYMENT.md               # 详细部署文档
└── web\                            # 前端文件
```

---

## 🔐 安全建议

1. **保护配置文件**: `coze_oauth_config.json` 包含敏感信息,确保文件权限正确
2. **使用 HTTPS**: 生产环境建议配置 SSL 证书
3. **限制访问**: 配置防火墙规则,只允许必要的 IP 访问
4. **定期更新**: 定期更新 Node.js 和依赖包
5. **备份数据**: 定期运行 `.\backup.ps1` 备份配置

---

## 📞 获取帮助

- **详细文档**: 查看 `coze_oauth_nodejs_jwt/DEPLOYMENT.md`
- **快速参考**: 查看 `coze_oauth_nodejs_jwt/QUICK_REFERENCE.md`
- **PM2 文档**: https://pm2.keymetrics.io/
- **Node.js 文档**: https://nodejs.org/

---

## ✅ 部署检查清单

- [ ] Git 已安装并配置
- [ ] Node.js 已安装(版本 >= 14)
- [ ] 代码已从 GitHub 克隆
- [ ] 配置文件已创建并填写正确信息
- [ ] 部署脚本已成功运行
- [ ] 服务状态正常(`pm2 status`)
- [ ] 可以访问服务(http://localhost:8080)
- [ ] 防火墙规则已配置(如需外网访问)
- [ ] Windows 服务已安装(如需开机自启)
- [ ] 健康检查通过(`.\health-check.ps1`)

---

祝部署顺利! 🎉

