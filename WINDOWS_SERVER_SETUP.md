# Windows 服务器快速部署步骤

## 📋 准备工作(在 Windows 服务器上执行)

### 1. 安装 Git
- 下载: https://git-scm.com/download/win
- 安装后验证: `git --version`

### 2. 安装 Node.js
- 下载: https://nodejs.org/ (选择 LTS 版本)
- 安装后验证: `node -v` 和 `npm -v`

---

## 🚀 部署步骤

### 第一步:克隆代码

打开 PowerShell,执行:

```powershell
# 进入工作目录(根据实际情况修改)
cd C:\

# 克隆仓库
git clone git@github.com:pleasureswx123/chat.git

# 如果 SSH 未配置,使用 HTTPS:
# git clone https://github.com/pleasureswx123/chat.git

# 进入项目目录
cd chat\coze_oauth_nodejs_jwt
```

### 第二步:配置应用

```powershell
# 1. 复制配置文件模板
Copy-Item coze_oauth_config.json.example coze_oauth_config.json

# 2. 编辑配置文件(填入你的实际配置)
notepad coze_oauth_config.json
```

**配置文件示例:**
```json
{
  "client_type": "jwt",
  "client_id": "你的客户端ID",
  "public_key_id": "你的公钥ID",
  "private_key": "-----BEGIN PRIVATE KEY-----\n你的私钥\n-----END PRIVATE KEY-----",
  "coze_www_base": "https://www.coze.cn",
  "coze_api_base": "https://api.coze.cn"
}
```

### 第三步:一键部署

```powershell
# 运行部署脚本(会自动安装 PM2、依赖并启动服务)
.\deploy.ps1
```

### 第四步:验证部署

```powershell
# 查看服务状态
pm2 status

# 查看日志
pm2 logs coze-oauth-service

# 健康检查
.\health-check.ps1
```

### 第五步:访问服务

打开浏览器访问: `http://localhost:8080`

---

## 🔧 可选:安装为 Windows 服务(开机自启)

```powershell
# 1. 以管理员身份运行 PowerShell
# 右键点击 PowerShell → "以管理员身份运行"

# 2. 进入项目目录
cd C:\chat\coze_oauth_nodejs_jwt

# 3. 安装 Windows 服务
.\install-service.ps1

# 4. 保存 PM2 进程列表
pm2 save
```

---

## 🌐 配置外网访问

### 1. 开放防火墙端口

```powershell
# 以管理员身份运行
New-NetFirewallRule -DisplayName "Coze OAuth Service" `
  -Direction Inbound `
  -LocalPort 8080 `
  -Protocol TCP `
  -Action Allow
```

### 2. 修改监听地址(如需要)

编辑 `index.js`,将监听地址改为 `0.0.0.0`:

```javascript
app.listen(port, '0.0.0.0', () => {
  console.log(`Server running on http://0.0.0.0:${port}`);
});
```

然后重启服务:
```powershell
pm2 restart coze-oauth-service
```

---

## 📊 常用管理命令

```powershell
# 查看服务状态
pm2 status

# 查看实时日志
pm2 logs coze-oauth-service

# 重启服务
pm2 restart coze-oauth-service

# 停止服务
pm2 stop coze-oauth-service

# 健康检查
.\health-check.ps1

# 备份配置
.\backup.ps1
```

---

## 🔄 更新代码

```powershell
# 1. 进入项目目录
cd C:\chat\coze_oauth_nodejs_jwt

# 2. 备份配置
.\backup.ps1

# 3. 拉取最新代码
git pull

# 4. 安装依赖
npm install --production

# 5. 重启服务
pm2 restart coze-oauth-service
```

---

## 🛠️ 常见问题

### Q1: Git 克隆失败 "Permission denied"
**解决**: 使用 HTTPS 方式克隆
```powershell
git clone https://github.com/pleasureswx123/chat.git
```

### Q2: PM2 命令找不到
**解决**: 重启 PowerShell 或手动添加到 PATH
```powershell
$npmPath = npm config get prefix
[Environment]::SetEnvironmentVariable("Path", $env:Path + ";$npmPath", "Machine")
```

### Q3: 端口 8080 被占用
**解决**: 查找并结束占用进程
```powershell
netstat -ano | findstr :8080
taskkill /PID <进程ID> /F
```

### Q4: 服务无法启动
**解决**: 查看错误日志
```powershell
pm2 logs coze-oauth-service --err
```

---

## 📚 详细文档

- **完整部署指南**: `coze_oauth_nodejs_jwt/DEPLOYMENT.md`
- **快速参考手册**: `coze_oauth_nodejs_jwt/QUICK_REFERENCE.md`
- **服务器部署指南**: `SERVER_DEPLOYMENT_GUIDE.md`

---

## ✅ 部署成功标志

- ✅ `pm2 status` 显示服务状态为 "online"
- ✅ 浏览器可以访问 http://localhost:8080
- ✅ `.\health-check.ps1` 所有检查通过
- ✅ 日志中没有错误信息

---

祝部署顺利! 🎉

如有问题,请查看详细文档或检查日志文件。

