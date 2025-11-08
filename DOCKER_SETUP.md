# Concourse CI - Docker 部署指南

## 📋 文件说明

本目录包含 Concourse CI 的 Docker Compose 部署配置文件：

| 文件 | 说明 | 来源 |
|------|------|------|
| `docker-compose.yml` | Docker Compose 配置文件 | [官方快速启动配置](https://concourse-ci.org/docker-compose.yml) |
| `docker-run.sh` | Docker Compose 启动脚本 | 本地创建 |
| `DOCKER_SETUP.md` | 本文档 | 本地创建 |

**下载日期**: 2025-11-08
**官方文档**: https://concourse-ci.org/
**GitHub 仓库**: https://github.com/concourse/concourse

---

## 🚀 快速开始

### 前置要求

- Docker 20.10+ (支持 `docker compose` 命令)
- 至少 4GB 可用内存
- 端口 8080 未被占用

### 启动服务

```bash
# 后台启动所有服务
./docker-run.sh up -d

# 查看服务状态
./docker-run.sh ps

# 查看实时日志
./docker-run.sh logs -f

# 查看特定服务的日志
./docker-run.sh logs -f concourse
./docker-run.sh logs -f concourse-db
```

### 访问 Concourse

启动成功后，访问:
- **Web UI**: http://localhost:8080
- **用户名**: `test`
- **密码**: `test`

---

## 🛠️ 常用命令

### 服务管理

```bash
# 启动服务
./docker-run.sh up -d

# 停止服务
./docker-run.sh down

# 重启服务
./docker-run.sh restart

# 停止并删除所有数据（包括数据库）
./docker-run.sh down -v
```

### 日志查看

```bash
# 查看所有服务日志
./docker-run.sh logs

# 实时跟踪日志
./docker-run.sh logs -f

# 查看最近 100 行日志
./docker-run.sh logs --tail=100

# 查看特定服务日志
./docker-run.sh logs -f concourse
./docker-run.sh logs -f concourse-db
```

### 服务状态

```bash
# 查看服务运行状态
./docker-run.sh ps

# 查看详细信息
docker compose ps -a

# 进入容器
docker compose exec concourse /bin/sh
docker compose exec concourse-db /bin/bash
```

---

## 📦 服务架构

### 服务组件

| 服务 | 镜像 | 端口 | 说明 |
|------|------|------|------|
| `concourse` | `concourse/concourse` | 8080 | Concourse Web + Worker |
| `concourse-db` | `postgres` | - | PostgreSQL 数据库 |

### 网络架构

```
┌─────────────────────────────────────┐
│  Concourse CI Docker 架构           │
├─────────────────────────────────────┤
│                                     │
│  ┌──────────────────────────────┐  │
│  │   concourse:8080             │  │
│  │  ┌──────────┬──────────┐     │  │
│  │  │ Web UI   │ Worker   │     │  │
│  │  └──────────┴──────────┘     │  │
│  └──────────────┬───────────────┘  │
│                 │                   │
│                 ▼                   │
│  ┌──────────────────────────────┐  │
│  │   concourse-db               │  │
│  │   PostgreSQL                 │  │
│  └──────────────────────────────┘  │
│                                     │
└─────────────────────────────────────┘
     ▲
     │ http://localhost:8080
     │
  浏览器访问
```

---

## ⚙️ 配置说明

### 环境变量

主要配置在 `docker-compose.yml` 的 `concourse` 服务中：

| 环境变量 | 默认值 | 说明 |
|----------|--------|------|
| `CONCOURSE_POSTGRES_HOST` | `concourse-db` | 数据库主机 |
| `CONCOURSE_POSTGRES_USER` | `concourse_user` | 数据库用户 |
| `CONCOURSE_POSTGRES_PASSWORD` | `concourse_pass` | 数据库密码 |
| `CONCOURSE_POSTGRES_DATABASE` | `concourse` | 数据库名称 |
| `CONCOURSE_EXTERNAL_URL` | `http://localhost:8080` | 外部访问地址 |
| `CONCOURSE_ADD_LOCAL_USER` | `test:test` | 默认用户 |
| `CONCOURSE_MAIN_TEAM_LOCAL_USER` | `test` | 主团队用户 |
| `CONCOURSE_CLUSTER_NAME` | `tutorial` | 集群名称 |

### 数据持久化

默认配置没有使用 volume 挂载，数据库数据存储在容器内部。

如果需要数据持久化，可以修改 `docker-compose.yml`：

```yaml
services:
  concourse-db:
    volumes:
      - postgres-data:/database

volumes:
  postgres-data:
```

---

## 🔧 自定义配置

### 修改端口

编辑 `docker-compose.yml`，修改端口映射：

```yaml
services:
  concourse:
    ports: ["3000:8080"]  # 将 Web UI 端口改为 3000
```

### 修改用户名密码

编辑 `docker-compose.yml`，修改环境变量：

```yaml
services:
  concourse:
    environment:
      CONCOURSE_ADD_LOCAL_USER: admin:your_password
      CONCOURSE_MAIN_TEAM_LOCAL_USER: admin
```

### 修改外部访问地址

```yaml
services:
  concourse:
    environment:
      CONCOURSE_EXTERNAL_URL: https://concourse.yourdomain.com
```

---

## 🐛 故障排查

### 服务无法启动

```bash
# 查看服务状态
./docker-run.sh ps

# 查看详细日志
./docker-run.sh logs

# 检查端口占用
lsof -i :8080
```

### 数据库连接失败

```bash
# 查看数据库日志
./docker-run.sh logs concourse-db

# 检查数据库健康状态
docker compose ps concourse-db

# 重启数据库服务
./docker-run.sh restart concourse-db
```

### 清理并重新开始

```bash
# 停止并删除所有容器和数据
./docker-run.sh down -v

# 清理 Docker 缓存（谨慎使用）
docker system prune -a

# 重新启动
./docker-run.sh up -d
```

---

## 📚 更多资源

- **官方文档**: https://concourse-ci.org/docs.html
- **GitHub 仓库**: https://github.com/concourse/concourse
- **Docker Hub**: https://hub.docker.com/r/concourse/concourse
- **社区教程**: https://concoursetutorial.com/

---

## 🔒 安全注意事项

⚠️ **生产环境使用前请注意**:

1. **修改默认密码**: 默认用户名密码是 `test:test`，请务必修改
2. **使用 HTTPS**: 配置反向代理（如 Nginx）提供 HTTPS 访问
3. **限制端口暴露**: 不要将 8080 端口直接暴露到公网
4. **数据备份**: 配置 PostgreSQL 数据库的定期备份
5. **更新镜像**: 定期更新 Concourse 和 PostgreSQL 镜像到最新版本

---

## 📝 版本信息

- **Concourse 镜像**: `concourse/concourse:latest`
- **PostgreSQL 镜像**: `postgres:latest`
- **Docker Compose 格式**: v3.x
- **平台架构**: `linux/amd64`

---

## 🤝 贡献

如有问题或改进建议，请提交 Issue 或 Pull Request。
