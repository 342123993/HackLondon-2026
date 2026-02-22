# 云服务器部署指南

## 架构

```
用户浏览器
    │
    ▼
Nginx (端口 80)
    ├── /         → frontend/dist/ 静态文件
    └── /api/     → backend:8000  FastAPI
                        │
                   MongoDB Atlas (云)
                   HiveMQ Cloud (云)
```

## 前提条件

服务器上安装 Docker 和 Docker Compose：
```bash
curl -fsSL https://get.docker.com | sh
apt install -y docker-compose-v2
```

## 部署步骤

### 1. 克隆代码到服务器
```bash
git clone <你的仓库地址> /opt/hacklondon
cd /opt/hacklondon
```

### 2. 构建前端（服务器上或本地后上传 dist/）
```bash
cd frontend
npm ci
npm run build
cd ..
```

### 3. 配置环境变量
```bash
cd cloud
cp .env.template .env
# 编辑 .env，填入 MongoDB URI、HiveMQ 密码、服务器 IP 等
nano .env
```

### 4. 启动服务
```bash
docker compose up -d --build
```

### 5. 验证
- 访问 `http://服务器IP` → 前端正常显示
- 访问 `http://服务器IP/api/seats` → 返回座位 JSON

## 常用命令

```bash
# 查看日志
docker compose logs -f

# 重启服务
docker compose restart

# 停止服务
docker compose down

# 更新代码后重新部署
git pull
cd frontend && npm ci && npm run build && cd ..
cd cloud && docker compose up -d --build
```

## 注意事项

- `.env` 文件含有数据库密码，不要提交到 git（已在 .gitignore 中排除）
- 前端 build 产物在 `frontend/dist/`，nginx 容器会直接挂载此目录
- 如需 HTTPS，可在 nginx 前加 certbot 或使用 Cloudflare 代理
