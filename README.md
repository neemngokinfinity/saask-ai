# OpenBot Self-Hosted Server — Docker Deployment (saask-ai)

Self-hosted [OpenBot](https://github.com/nightly-labs/openbot) server deployment config
for a VPS (Ubuntu 24.04 + Docker Engine 29 + Docker Compose v2).

## What's in this repo

| File | Purpose |
| --- | --- |
| `compose.yaml` | Official OpenBot Docker Compose file (from the upstream repo, unchanged) |
| `compose.override.yaml` | Memory limit override: caps the container at 4 GB |
| `seccomp.json` | Official seccomp profile so the Electron sandbox user namespaces work |

## Prerequisites

- Linux VPS (Ubuntu 24.04 / 26.04 recommended), x86_64 or arm64
- Docker Engine + Docker Compose v2
- An OpenBot account (email + one-time code)

## 1. Install

```bash
mkdir -p ~/openbot-docker && cd ~/openbot-docker
curl -fsSLO https://raw.githubusercontent.com/nightly-labs/openbot/main/docker/compose.yaml
curl -fsSLO https://raw.githubusercontent.com/nightly-labs/openbot/main/docker/seccomp.json
# add compose.override.yaml (4 GB memory limit) from this repo
docker compose up -d
```

## 2. Sign in

```bash
docker compose exec openbot openbot login
```

- Enter your email, then the one-time code sent to it.
- Verify the server shows online: `docker compose exec openbot openbot status`

## 3. Connect your mobile phone

```bash
docker compose exec openbot openbot mobile-connect
```

- Open the OpenBot app → **Scan QR code** → scan the code in the terminal.
- The QR works for one sign-in and expires after ~2 minutes.

## 4. Common commands

| Task | Command |
| --- | --- |
| Status / version | `docker compose exec openbot openbot status` |
| Logs (follow) | `docker compose logs -f openbot` |
| Restart | `docker compose restart openbot` |
| Stop | `docker compose stop openbot` |
| Start | `docker compose start openbot` |
| Upgrade | `docker compose pull && docker compose up -d` |

## Security notes

- The container publishes **no public port** (only outbound connections).
- seccomp profile + `no-new-privileges` keep the Electron sandbox enabled.
- Data persists in the named volume `openbot-data`.
- Agents run inside the container with full access to it — keep the box private and trusted.
