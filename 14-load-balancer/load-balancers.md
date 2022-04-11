# Load-balancers

In Kubernetes load-balancers are not only responsible for the distribution of network traffic but also for the assingment of external IP's for your services.

## MetalLB installation

MetalLB is a load-balancer that works with bare-metal Kubernetes deployments. Other load-balancers are usually provided by the respective cloud providers eg. Openstack, Azure etc.

We will use Helm to install MetalLB. In order to provide IP addresses that can be later assigned to our services we will utilize the private network that minikube created for it's deployment. First let's get minikube's IP address:

```console
minikube ip
```

Then edit `values.yaml` and change the address range to one that fits inside minikube's network. E.g. if the output of the previous command yielded 192.168.49.2 you can safely assume that you may use the address space from 192.168.49.8-192.168.49.16 etc.

Add MetalLB's Helm repository:

```console
helm repo add metallb https://metallb.github.io/metallb
```

Then proceed with the installation:

```console
helm install metallb metallb/metallb -f values.yaml
```
