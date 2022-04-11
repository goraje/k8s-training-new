# Role-based authentication

## Generate the necessary certifiactes

Create a key:

```console
openssl genrsa -out professorX.key 2048
```

Create a CSR:

```console
openssl req -new -key professorX.key -out professorX.csr -subj "/CN=professorX/O=XMen"
```

Sign the CSR with minikube's root CA:

```console
openssl x509 -req -in professorX.csr -CA <path-to-root-CA> -CAkey <path-to-root-CA-key> -CAcreateserial -out professorX.crt -days 365
```

Create the role and rolebinding:

```console
kubectl apply -f role.yaml
kubectl apply -f roleBinding.yaml
```

Set the credentials for the new user inside kubectl:

```console
kubectl config set-credentials professorX --client-certificate=professorX.crt --client-key=professorX.key
```

Create a kubectl context:

```console
kubectl config set-context xmen --cluster=minikube --user=professorX
```

Switch to the new context:

```console
kubectl config use-context xmen
```

Test the privileges with:

```console
kubectl get svc -n project-x
kubectl get pod -n project-x
```
