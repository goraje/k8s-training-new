# Environment setup

## Running a local Docker registry

### Hosts file setup

Edit your hosts file:
- Linux - /etc/hosts
- Windows - C:\Windows\System32\drivers\etc\hosts

and add the following line:

```console
127.0.0.1 registry.dev.svc.cluster.local
```

### Docker setup

Edit or create:
- Linux - /etc/docker/daemon.json
- Windows - C:\ProgramData\Docker\config\daemon.json or via DockerDesktop

Make sure that the following entry is present in the daemon.json:

```json
{
    "insecure-registries": [
        "registry.dev.svc.cluster.local:5000"
    ]
}
```

Restart your Docker daemon:
- via the DockerDesktop client on Windows
- via `sudo service docker.service restart` on WSL2
- via `sudo systemctl restart docker.service` on Linux

Make sure that the changes are recognized by Docker by running:

```console
docker info
```

Towards the bottom of the output you should find the following entry:

```console
 Insecure Registries:
  registry.dev.svc.cluster.local:5000
```

### Minikube setup

Stop and delete any minikube instances:

```console
minikube stop
minikube delete
```

Start a new minikube instance passing the `--ports` and `--insecure-registry` flags:

```console
minikube start --ports=127.0.0.1:5000:5000 --insecure-registry="registry.dev.svc.cluster.local"
```

Validate minikube's Docker configuration by running:

```console
minikube ssh 'docker info'
```

Towards the bottom of the output you should find the following entry:

```console
 Insecure Registries:
  registry.dev.svc.cluster.local:5000
```

### Start the local registry

Start the local Docker registry on minikube by running:

```console
minikube ssh 'docker run -d -p 5000:5000 --restart=always --name registry registry:2'
```

Validate the connectibility to the registry by running:

```console
curl http://registry.dev.svc.cluster.local:5000/v2/_catalog
```

The output should read:

```console
{"repositories":[]}
```
