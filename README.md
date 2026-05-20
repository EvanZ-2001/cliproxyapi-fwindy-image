# CLIProxyAPI Fwindy Image

[中文说明](README_CN.md)

This repository builds Docker images from `Fwindy/CLIProxyAPI` on GitHub
Actions and publishes them to:

```text
ghcr.io/evanz-2001/cliproxyapi-fwindy
```

It is intended for deployment machines where building CLIProxyAPI from Go
source is too slow or too resource-intensive. The included `compose.yml` pulls
the prebuilt image from GHCR and does not contain a local `build:` section.

The scheduled workflow resolves the latest `v*` tag from
`Fwindy/CLIProxyAPI`, refreshes the models catalog from
`router-for-me/models`, and pushes multi-architecture images for:

- `linux/amd64`
- `linux/arm64`

## Tags

- `latest`: latest resolved `v*` tag
- `vX.Y.Z`: exact upstream fork tag
- `sha-xxxxxxx`: exact source commit

## Prerequisites

- Docker
- Docker Compose v2

The image is public, so `docker login ghcr.io` is not required for normal
pulls.

## Quick Deploy

Clone this deployment repository:

```bash
git clone https://github.com/EvanZ-2001/cliproxyapi-fwindy-image.git
cd cliproxyapi-fwindy-image
```

Create local configuration files before starting the container:

```bash
cp config.example.yaml config.yaml
cp .env.example .env
mkdir -p auths logs
```

Edit `config.yaml` for your API keys, management settings, providers, and other
CLIProxyAPI options. The `.env` file is optional for basic local deployment, but
it is useful for pinning the image tag or customizing host paths.

Start the service:

```bash
docker compose pull
docker compose up -d
```

Check logs:

```bash
docker compose logs -f
```

Stop the service:

```bash
docker compose down
```

## Pin A Version

For repeatable deployments, prefer a fixed version tag instead of `latest`.

Edit `.env` and set:

```bash
CLI_PROXY_IMAGE=ghcr.io/evanz-2001/cliproxyapi-fwindy:v7.1.17
```

Then run:

```bash
docker compose pull
docker compose up -d
```

You can also set it inline:

```bash
CLI_PROXY_IMAGE=ghcr.io/evanz-2001/cliproxyapi-fwindy:v7.1.17 docker compose up -d
```

## Paths

The default `compose.yml` uses these host paths:

```text
./config.yaml -> /CLIProxyAPI/config.yaml
./auths       -> /root/.cli-proxy-api
./logs        -> /CLIProxyAPI/logs
```

Override them in `.env` when needed:

```bash
CLI_PROXY_CONFIG_PATH=/opt/cliproxyapi/config.yaml
CLI_PROXY_AUTH_PATH=/opt/cliproxyapi/auths
CLI_PROXY_LOG_PATH=/opt/cliproxyapi/logs
```

Make sure `config.yaml` exists before running `docker compose up`; otherwise
Docker may create a directory at that path.

## Upgrade

If you use `latest`:

```bash
docker compose pull
docker compose up -d
```

If you pin a version, update `CLI_PROXY_IMAGE` in `.env`, then run the same two
commands.

## Manual Image Build

Run the `build-fwindy-cliproxyapi-image` workflow manually. Leave `ref` empty
to build the latest `v*` tag, or set it to a tag, branch, or commit from
`Fwindy/CLIProxyAPI`.
