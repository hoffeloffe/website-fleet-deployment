# my-website — Fleet Deployment

GitOps manifests for deploying [my-website](https://hub.docker.com/r/hoffeloffe/my-website)
to a Kubernetes (Rancher) homelab cluster using
[Rancher Fleet](https://fleet.rancher.io/).

Fleet watches this repo and reconciles the cluster to match the manifests under
[`fleet/`](./fleet) — no manual `kubectl apply`.

## What gets deployed

Namespace: `my-website`

| Manifest | Resource | Notes |
| --- | --- | --- |
| [`fleet/deployment.yaml`](./fleet/deployment.yaml) | `Deployment` my-website | 2 replicas, tcp probes, CPU/memory requests + limits |
| [`fleet/service.yaml`](./fleet/service.yaml) | `Service` my-website-service | ClusterIP on port 80 |
| [`fleet/ingress.yaml`](./fleet/ingress.yaml) | `Ingress` my-website-ingress | host `hoff3.net` |
| [`fleet/fleet.yaml`](./fleet/fleet.yaml) | Fleet bundle config | `defaultNamespace: my-website` |

The container image is **pinned by digest** (not `:latest`) so deploys are
reproducible and rollback-able. Bump the digest when a new image is published.

## Deploying a new version

1. Build & push a new `hoffeloffe/my-website` image.
2. Update the image digest in `fleet/deployment.yaml` and commit.
3. Fleet detects the change and rolls it out.

## Local validation

```bash
kubeconform -strict -ignore-missing-schemas \
  fleet/deployment.yaml fleet/service.yaml fleet/ingress.yaml
```
