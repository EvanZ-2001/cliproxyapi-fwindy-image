# CLIProxyAPI Fwindy Image

This repository builds Docker images from `Fwindy/CLIProxyAPI` on GitHub
Actions and publishes them to:

```text
ghcr.io/evanz-2001/cliproxyapi-fwindy
```

The scheduled workflow resolves the latest `v*` tag from
`Fwindy/CLIProxyAPI`, refreshes the models catalog from
`router-for-me/models`, and pushes multi-architecture images for:

- `linux/amd64`
- `linux/arm64`

## Tags

- `latest`: latest resolved `v*` tag
- `vX.Y.Z`: exact upstream fork tag
- `sha-xxxxxxx`: exact source commit

## Manual Build

Run the `build-fwindy-cliproxyapi-image` workflow manually. Leave `ref` empty
to build the latest `v*` tag, or set it to a tag, branch, or commit from
`Fwindy/CLIProxyAPI`.

## Deploy

Use the included `compose.yml` on machines where local source builds should be
avoided:

```bash
docker compose pull
docker compose up -d
```

Pin a specific version with:

```bash
CLI_PROXY_IMAGE=ghcr.io/evanz-2001/cliproxyapi-fwindy:v7.1.17 docker compose up -d
```
