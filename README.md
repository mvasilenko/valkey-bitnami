# valkey-bitnami

Valkey Sentinel Helm chart with OSS Valkey 9.x images.

Bitnami 4.0.2 chart wrapper with:
- Sentinel HA, `createPrimary: true` (primary-tracking ClusterIP via `kubectl-shared` sidecar)
- Projected-volume TLS CA injection (`tls.caSecretName`) for split CA/cert-manager setups
- Custom images built from official `valkey/valkey` OSS binaries + Bitnami-compatible scripts

## Images

Built and pushed to GHCR on git tag (`v<VALKEY_VERSION>`):

| Image | Tag |
|---|---|
| `ghcr.io/mvasilenko/valkey` | valkey engine version (e.g. `9.0.3`) |
| `ghcr.io/mvasilenko/valkey-sentinel` | valkey engine version (e.g. `9.0.3`) |

To build a new version: `git tag v9.0.3 && git push origin v9.0.3`

## Install

```bash
# Default: bitnamilegacy/valkey:8.1.3 (no build required)
helm install valkey-bitnami-oss . -n <namespace>

# With custom 9.x images
helm install valkey-bitnami-oss . -n <namespace> \
  --set valkey.image.registry=ghcr.io \
  --set valkey.image.repository=mvasilenko/valkey \
  --set valkey.image.tag=9.0.3 \
  --set valkey.sentinel.image.registry=ghcr.io \
  --set valkey.sentinel.image.repository=mvasilenko/valkey-sentinel \
  --set valkey.sentinel.image.tag=9.0.3
```

Auth secret must exist before install:
```bash
kubectl create secret generic valkey-bitnami-oss-credentials \
  --from-literal=password=<password> -n <namespace>
```

## Structure

```
.
├── Chart.yaml / values.yaml / templates/   ← wrapper chart (this repo root is the chart)
├── charts/valkey/                          ← Bitnami 4.0.2 vendored subchart
└── docker/
    ├── valkey/                             ← Dockerfile + Bitnami scripts
    └── valkey-sentinel/                    ← Dockerfile + Bitnami scripts
```
