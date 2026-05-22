# OpenClaw Infra 🌀

Infraestructura de OpenClaw desplegada en el cluster k3s de Kelvin.

## Stack

- **OpenClaw Gateway** — Agente principal (namespace: `openclaw`)
- **OpenClaw Runner** — Sandbox para ejecución aislada

## Despliegue

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/secrets.yaml      # asegúrate de tenerlo configurado
kubectl apply -f k8s/configmaps.yaml
kubectl apply -f k8s/services.yaml
kubectl apply -f k8s/deployments.yaml
```

## Estructura

```
k8s/            → Manifiestos de Kubernetes
config/         → Archivos de configuración
```

## Notas

- Los secrets no se commitean. Usa `secrets.yaml.example` como template.
- Cluster: k3s v1.33.6 en Raspberry Pi 5
