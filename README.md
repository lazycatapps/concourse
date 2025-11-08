# Concourse CI

<div align="center">

**现代化的 CI/CD 流水线工具**

基于容器的持续集成和持续交付自动化系统

[官方网站](https://concourse-ci.org/) · [问题反馈](https://github.com/lazycatapps/concourse/issues) · [社区版仓库](https://github.com/concourse/concourse)

</div>

---

## 简介

Concourse 是一个基于容器的自动化系统，使用 Go 语言编写，主要用于持续集成（CI）和持续交付（CD）。它通过可扩展的流水线（Pipeline）模型来定义和运行各种自动化任务，从最简单的构建脚本到复杂的多阶段部署流程。

本仓库是 Concourse CI 在 **Lazycat Apps** 平台上的移植版本，经过优化和配置，可在 Lazycat 平台上一键部署和使用。

Concourse 的设计理念强调：
- **幂等性**：任务可重复执行，结果一致
- **不可变性**：容器化环境保证构建环境的一致性
- **声明式配置**：所有流水线均通过 YAML 定义
- **无状态工作节点**：Worker 节点不保存状态，可随意扩展
- **可重现的构建**：相同的输入产生相同的输出

## 主要功能

- 🔄 **流水线即代码**
  所有工作流都在 YAML 文件中定义，使流水线可版本控制、易于共享和协作

- 🐳 **容器优先**
  原生支持容器技术，所有任务都在 Docker 镜像中运行，确保环境一致性

- 📦 **资源抽象**
  统一的资源接口，支持 Git、S3、Docker 镜像、时间触发器等多种输入输出类型

- 🔧 **强大的调试工具**
  内置 `fly execute` 本地运行任务、`fly intercept` 调试容器，快速定位问题

- ♻️ **幂等性保证**
  任务设计为短暂且幂等，确保构建的可重现性和可靠性

- 🌐 **Web UI**
  直观的可视化界面，实时查看流水线状态和任务执行情况

- 🔐 **访问控制**
  支持多团队管理和精细的权限控制，保障企业级安全需求

- 📊 **并行执行**
  自动并行化独立任务，充分利用系统资源，提高构建效率

## 快速开始

### 在 Lazycat 平台部署

访问 Lazycat Apps 应用商店，搜索 "Concourse CI"，点击一键部署即可。

### 访问 Web UI

部署成功后，访问分配的应用地址（默认端口 8080），使用以下默认凭证登录：

- **用户名**: `admin`
- **密码**: `admin`

> ⚠️ 安全提示：生产环境请务必修改默认密码

### 安装 Fly CLI

1. 访问 Concourse Web UI
2. 点击右上角下载对应系统的 `fly` CLI 工具
3. 将下载的文件放入系统 PATH 中

### 创建你的第一个流水线

1. 创建一个 `pipeline.yml` 文件：

```yaml
jobs:
- name: hello-world
  plan:
  - task: say-hello
    config:
      platform: linux
      image_resource:
        type: registry-image
        source:
          repository: ghcr.io/hlesey/busybox
      run:
        path: sh
        args:
        - -c
        - |
          echo "Hello, Concourse!"
          echo "Hello, LazyCAT!"
```

2. 登录到 Concourse：

```bash
fly -t tutorial login -c <应用地址> -u admin -p admin
```

3. 设置流水线：

```bash
fly -t tutorial set-pipeline -p my-first-pipeline -c pipeline.yml
```

4. 启用流水线：

```bash
fly -t tutorial unpause-pipeline -p my-first-pipeline
```

5. 触发流水线执行：

```bash
fly -t tutorial trigger-job -j my-first-pipeline/hello-world --watch
```

## 配置说明

### 环境变量

| 变量名 | 默认值 | 说明 |
|--------|--------|------|
| `CONCOURSE_EXTERNAL_URL` | `https://${LAZYCAT_APP_DOMAIN}` | 外部访问地址 |
| `CONCOURSE_ADD_LOCAL_USER` | `admin:admin` | 本地用户凭证 |
| `CONCOURSE_POSTGRES_HOST` | `concourse-db` | PostgreSQL 主机地址 |
| `CONCOURSE_WORKER_RUNTIME` | `containerd` | Worker 运行时 |

### 系统要求

- **内存**: 建议至少 2GB
- **存储**: 数据库数据会持久化存储
- **网络**: 需要访问容器镜像仓库

## 注意事项

- ⚠️ **资源需求**：Concourse 需要一定的系统资源，建议至少 2GB 内存
- 🔒 **安全提示**：默认密码为 admin/admin，生产环境请通过修改环境变量更改密码
- 📝 **持久化**：数据库数据已配置持久化存储，卸载应用前请注意备份
- 🌐 **外部 URL**：如需配置外部访问地址，请修改 `CONCOURSE_EXTERNAL_URL` 环境变量
- 🐳 **镜像下载**：部分容器镜像可能从 Google 镜像仓库下载，网络不佳时可能较慢

## 相关链接

- **Concourse 官方网站**: https://concourse-ci.org/
- **官方文档**: https://concourse-ci.org/docs.html
- **社区版仓库**: https://github.com/concourse/concourse
- **Lazycat Apps 平台**: https://lazycat.cloud/
- **本仓库**: https://github.com/lazycatapps/concourse

## 致谢

本项目基于 [Concourse](https://github.com/concourse/concourse) 开源项目移植和优化。

特别感谢：
- Concourse 社区及所有贡献者
- Broadcom Inc. / VMware, Inc. 对 Concourse 项目的支持
- Lazycat Apps 平台提供的部署支持

## 版权说明

本仓库的移植和配置工作：

```
Copyright (c) <year> Lazycat Apps
```

采用 Apache License 2.0 许可证，详见 [LICENSE](./LICENSE) 文件。

原始 Concourse 项目版权归 Concourse 社区所有，同样采用 Apache License 2.0 许可证。

---

<div align="center">

**由 Lazycat Apps 移植和维护**

Made with ❤️ for the DevOps community

</div>
