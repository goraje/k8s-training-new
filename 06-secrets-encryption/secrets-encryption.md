# Secrets encryption

Read about how Kubernetes encrypts its `Secrets` [here](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/).


## Check `Secret` encryption

Switch to the `default` namespace context:

```console
kubectl config set-context --current --namespace=default
```

Create a simple `Secret`:

```console
kubectl create secret generic secret1 -n default --from-literal=mykey=mydata
```

Check etcd's listening port:

```console
minikube ssh 'ps aux | grep etcd'
```

Search for the etcd entry of the created `Secret`:

```console
 kubectl exec -it etcd-minikube -n kube-system -- etcdctl --endpoints <etcd-listening-port> --cacert /var/lib/minikube/certs/etcd/ca.crt --cert /var/lib/minikube/certs/etcd/server.crt --key /var/lib/minikube/certs/etcd/server.key get /registry/secrets/default/secret1
```

> **IMPORTANT** Notice that the `Secret` is stored as plain text at the moment.

Delete the `Secret` before proceeding

```console
kubectl delete secret secret1
```

## Create `Secret` encryption rules

Connect to minikube via ssh:

```console
minikube ssh
```

Create a directory to store the configuration file:

```console
sudo mkdir /var/lib/minikube/certs/encrypt
```

Paste the contents of `encryptionConfiguration.yaml` to the newly created directory:

```console
sudo su
cat <<EOF>> /var/lib/minikube/certs/encrypt/encryptionConfiguration.yaml
> (paste the file content)
EOF
```

Exit and stop the minikube instance:

```console
minikube stop
```

Start the minikube instance with the following command

```console
 minikube start --ports=127.0.0.1:5000:5000 --insecure-registry="registry.dev.svc.cluster.local" --extra-config=apiserver.encryption-provider-config=/var/lib/minikube/certs/encrypt/encryptionConfiguration.yaml
```

Repeat the creation of the `Secret` and check for it's etcd entry as in the previous section:

```console
kubectl create secret generic secret1 -n default --from-literal=mykey=mydata
kubectl exec -it etcd-minikube -n kube-system -- etcdctl --endpoints <etcd-listening-port> --cacert /var/lib/minikube/certs/etcd/ca.crt --cert /var/lib/minikube/certs/etcd/server.crt --key /var/lib/minikube/certs/etcd/server.key get /registry/secrets/default/secret1
```

> **IMPORTANT** Notice that now the `Secret` is properly encrypted and that the encryption algorithm is present in the header of the etcd entry.
