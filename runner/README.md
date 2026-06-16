# Runner — OpenClaw Sandbox

Imagen Docker del sandbox runner para OpenClaw, desplegado en k3s sobre Raspberry Pi 5 (ARM64).

## Contenido

| Archivo | Descripción |
|---|---|
| `Dockerfile` | Imagen Alpine ARM64 con SSH, Docker CLI, kubectl, Codex CLI |
| `authorized_keys` | Claves SSH públicas para acceso al runner |
| `build-job.yaml` | Job de Kubernetes para build local en el cluster (RPi5 nativo) |
| `k8s/deployment.yaml` | Deployment/Rollout del runner en k8s |
| `k8s/service.yaml` | Service del runner en k8s |

## Tools incluidas

- OpenSSH server (acceso como `runner`)
- Docker CLI + docker-compose
- kubectl (ARM64)
- Git, Bash, Python 3, pip
- Node.js 22 + npm
- **OpenAI Codex CLI** (`/usr/bin/codex`)
- Curl, jq, ca-certificates

## Build local (RPi5)

```bash
# Usando el job de k8s (recomendado)
kubectl apply -f build-job.yaml

# O manual
docker build -t silencfox/openclaw:runner .
docker push silencfox/openclaw:runner
```

## GitHub Actions (multi-arch)

El workflow `.github/workflows/build-runner.yml` hace build y push automático cuando se modifican archivos del runner.

**Requisito:** Configurar estos secrets en GitHub:
- `DOCKERHUB_USERNAME` = `silencfox`
- `DOCKERHUB_TOKEN` = PAT de DockerHub con scope read/write
