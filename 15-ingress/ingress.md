# Ingress controller

Ingress controllers route the networking traffic inside the Kubernetes network.

## Traefik ingress installation

You can read all about Traefik [here](https://doc.traefik.io/traefik/).

First let's add traefik's repo to helm:

```console
helm repo add traefik https://helm.traefik.io/traefik
```

Then proceed with the installation:

```console
helm install traefik traefik/traefik -n traefik
```
