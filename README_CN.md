# CLIProxyAPI Fwindy 镜像

[English](README.md)

这个仓库用于从 `Fwindy/CLIProxyAPI` 自动构建 Docker 镜像，并发布到：

```text
ghcr.io/evanz-2001/cliproxyapi-fwindy
```

它的目标是让性能较弱的机器不再从 Go 源码构建 CLIProxyAPI。仓库里的
`compose.yml` 只会拉取 GHCR 上的预构建镜像，不包含本地 `build:` 配置。

GitHub Actions 会定时解析 `Fwindy/CLIProxyAPI` 的最新 `v*` tag，刷新
`router-for-me/models` 的模型目录，然后构建并推送以下架构的镜像：

- `linux/amd64`
- `linux/arm64`

## 镜像标签

- `latest`：当前解析到的最新 `v*` tag
- `vX.Y.Z`：Fwindy fork 的明确版本 tag
- `sha-xxxxxxx`：明确源码 commit

## 前置要求

- Docker
- Docker Compose v2

镜像是公开的，普通拉取不需要执行 `docker login ghcr.io`。

## 快速部署

克隆这个部署仓库：

```bash
git clone https://github.com/EvanZ-2001/cliproxyapi-fwindy-image.git
cd cliproxyapi-fwindy-image
```

启动容器前先创建本地配置文件：

```bash
cp config.example.yaml config.yaml
cp .env.example .env
mkdir -p auths logs static
```

根据你的实际需求编辑 `config.yaml`，例如 API key、管理 API、provider、代理等
CLIProxyAPI 配置。基础本地部署不一定需要 `.env`，但它适合用来固定镜像版本或修改
挂载路径。

启动服务：

```bash
docker compose pull
docker compose up -d
```

查看日志：

```bash
docker compose logs -f
```

停止服务：

```bash
docker compose down
```

## 固定版本

日常使用建议固定版本 tag，不要长期依赖 `latest`。

编辑 `.env`，设置：

```bash
CLI_PROXY_IMAGE=ghcr.io/evanz-2001/cliproxyapi-fwindy:v7.1.17
```

然后执行：

```bash
docker compose pull
docker compose up -d
```

也可以临时指定：

```bash
CLI_PROXY_IMAGE=ghcr.io/evanz-2001/cliproxyapi-fwindy:v7.1.17 docker compose up -d
```

## 文件挂载

默认 `compose.yml` 使用这些宿主机路径：

```text
./config.yaml -> /CLIProxyAPI/config.yaml
./auths       -> /root/.cli-proxy-api
./logs        -> /CLIProxyAPI/logs
./static      -> /CLIProxyAPI/static
```

如果需要自定义路径，可以在 `.env` 中设置：

```bash
CLI_PROXY_CONFIG_PATH=/opt/cliproxyapi/config.yaml
CLI_PROXY_AUTH_PATH=/opt/cliproxyapi/auths
CLI_PROXY_LOG_PATH=/opt/cliproxyapi/logs
CLI_PROXY_STATIC_PATH=/opt/cliproxyapi/static
```

运行 `docker compose up` 前请确保 `config.yaml` 已经存在，否则 Docker 可能会在该路径
创建一个目录，导致配置挂载不符合预期。

如果 `static/management.html` 已存在，CLIProxyAPI 会直接提供管理面板静态文件。如果它
不存在，CLIProxyAPI 会在首次访问时根据当前网络和代理配置尝试下载管理面板。

## 更新

如果你使用 `latest`：

```bash
docker compose pull
docker compose up -d
```

如果你固定了版本，先修改 `.env` 中的 `CLI_PROXY_IMAGE`，再执行同样的两条命令。

## 手动构建镜像

在 GitHub Actions 页面手动运行 `build-fwindy-cliproxyapi-image` workflow。`ref` 留空
时会构建 `Fwindy/CLIProxyAPI` 最新 `v*` tag；也可以填写 Fwindy 仓库中的某个 tag、
branch 或 commit。
