# Coze OAuth Chat Application

基于 Coze API 的 OAuth 认证聊天应用,包含 Node.js 后端服务和 Web 前端。

## 📁 项目结构

```
chat/
├── coze_oauth_nodejs_jwt/          # Node.js 后端服务
│   ├── index.js                    # 服务入口
│   ├── package.json                # 依赖配置
│   ├── ecosystem.config.js         # PM2 配置
│   ├── deploy.ps1                  # 一键部署脚本
│   ├── install-service.ps1         # Windows 服务安装
│   ├── health-check.ps1            # 健康检查
│   ├── backup.ps1                  # 备份脚本
│   ├── DEPLOYMENT.md               # 详细部署文档
│   ├── QUICK_REFERENCE.md          # 快速参考
│   └── websites/                   # 静态资源
├── web/                            # Web 前端
│   └── index.html                  # 聊天界面
├── WINDOWS_SERVER_SETUP.md         # Windows 服务器快速部署指南 ⭐
├── SERVER_DEPLOYMENT_GUIDE.md      # 服务器部署完整指南
└── README.md                       # 本文件
```

## 🚀 快速开始

### Windows 服务器部署

**详细步骤请查看**: [WINDOWS_SERVER_SETUP.md](./WINDOWS_SERVER_SETUP.md) ⭐

```powershell
# 1. 克隆仓库
git clone git@github.com:pleasureswx123/chat.git
cd chat\coze_oauth_nodejs_jwt

# 2. 配置应用
Copy-Item coze_oauth_config.json.example coze_oauth_config.json
notepad coze_oauth_config.json  # 填入你的配置

# 3. 一键部署
.\deploy.ps1

# 4. 验证
pm2 status
```

### 本地开发

```bash
# Mac/Linux
cd coze_oauth_nodejs_jwt
sh bootstrap.sh

# Windows
cd coze_oauth_nodejs_jwt
.\bootstrap.ps1
```

## 📚 文档导航

| 文档 | 说明 | 适用场景 |
|------|------|----------|
| [WINDOWS_SERVER_SETUP.md](./WINDOWS_SERVER_SETUP.md) | **Windows 服务器快速部署** ⭐ | 首次部署必读 |
| [SERVER_DEPLOYMENT_GUIDE.md](./SERVER_DEPLOYMENT_GUIDE.md) | 服务器部署完整指南 | 详细部署流程 |
| [coze_oauth_nodejs_jwt/DEPLOYMENT.md](./coze_oauth_nodejs_jwt/DEPLOYMENT.md) | 三种部署方案详解 | 选择部署方案 |
| [coze_oauth_nodejs_jwt/QUICK_REFERENCE.md](./coze_oauth_nodejs_jwt/QUICK_REFERENCE.md) | 常用命令速查 | 日常运维 |

## ✨ 功能特性

- ✅ JWT OAuth 认证
- ✅ Coze API 集成
- ✅ Web 聊天界面
- ✅ PM2 进程管理
- ✅ Windows 服务支持
- ✅ 自动健康检查
- ✅ 日志管理
- ✅ 配置备份

## 🛠️ 技术栈

- **后端**: Node.js + Koa
- **认证**: JWT OAuth
- **进程管理**: PM2
- **API**: Coze API (@coze/api)
- **前端**: HTML + JavaScript + Coze Web SDK

## 📋 环境要求

- Node.js >= 14
- npm 或 pnpm
- Git
- Windows Server / Windows 10+ (生产环境)

## 🔧 配置说明

配置文件 `coze_oauth_config.json`:

```json
{
  "client_type": "jwt",
  "client_id": "你的客户端ID",
  "public_key_id": "你的公钥ID",
  "private_key": "-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----",
  "coze_www_base": "https://www.coze.cn",
  "coze_api_base": "https://api.coze.cn"
}
```

## 📊 常用命令

```powershell
# 服务管理
pm2 status                          # 查看状态
pm2 logs coze-oauth-service         # 查看日志
pm2 restart coze-oauth-service      # 重启服务

# 健康检查
.\health-check.ps1                  # 运行健康检查

# 备份
.\backup.ps1                        # 备份配置

# 更新
git pull                            # 拉取最新代码
npm install --production            # 安装依赖
pm2 restart coze-oauth-service      # 重启服务
```

## 🔐 安全建议

1. **保护配置文件**: `coze_oauth_config.json` 包含敏感信息,已添加到 `.gitignore`
2. **使用 HTTPS**: 生产环境建议配置 SSL 证书
3. **限制访问**: 配置防火墙规则
4. **定期更新**: 及时更新依赖包
5. **定期备份**: 使用 `.\backup.ps1` 定期备份

## 🐛 故障排查

### 服务无法启动
```powershell
pm2 logs coze-oauth-service --err
```

### 端口被占用
```powershell
netstat -ano | findstr :8080
taskkill /PID <进程ID> /F
```

### PM2 命令找不到
```powershell
npm config get prefix
# 将输出路径添加到系统 PATH
```

更多问题请查看 [DEPLOYMENT.md](./coze_oauth_nodejs_jwt/DEPLOYMENT.md)

## 📞 获取帮助

- **快速部署**: [WINDOWS_SERVER_SETUP.md](./WINDOWS_SERVER_SETUP.md)
- **详细文档**: [DEPLOYMENT.md](./coze_oauth_nodejs_jwt/DEPLOYMENT.md)
- **命令速查**: [QUICK_REFERENCE.md](./coze_oauth_nodejs_jwt/QUICK_REFERENCE.md)
- **Coze API**: https://www.coze.cn/docs
- **PM2 文档**: https://pm2.keymetrics.io/

## 📝 License

MIT

## 🤝 贡献

欢迎提交 Issue 和 Pull Request!

---

**开始部署**: 查看 [WINDOWS_SERVER_SETUP.md](./WINDOWS_SERVER_SETUP.md) 📖

