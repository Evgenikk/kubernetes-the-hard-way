# Configuring kubectl for Remote Access

In this lab you will generate a kubeconfig file for the `kubectl` command line utility based on the `admin` user credentials.

> Run the commands in this lab from the same directory used to generate the admin client certificates.

## The Admin Kubernetes Configuration File

Each kubeconfig requires a Kubernetes API Server to connect to. To support high availability the IP address assigned to the external load balancer fronting the Kubernetes API Servers will be used.

Generate a kubeconfig file suitable for authenticating as the `admin` user:

```
{
  KUBERNETES_PUBLIC_ADDRESS=$(gcloud compute addresses describe kubernetes-the-hard-way \
    --region $(gcloud config get-value compute/region) \
    --format 'value(address)')

  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_PUBLIC_ADDRESS}:6443

  kubectl config set-credentials admin \
    --client-certificate=admin.pem \
    --client-key=admin-key.pem

  kubectl config set-context kubernetes-the-hard-way \
    --cluster=kubernetes-the-hard-way \
    --user=admin

  kubectl config use-context kubernetes-the-hard-way
}
```

## Verification

Check the health of the remote Kubernetes cluster:

```
kubectl get componentstatuses
```

> output

```
NAME                 STATUS    MESSAGE             ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-1               Healthy   {"health":"true"}
etcd-2               Healthy   {"health":"true"}
etcd-0               Healthy   {"health":"true"}
```

List the nodes in the remote Kubernetes cluster:

```
kubectl get nodes -o wide
```

> output

```
NAME       STATUS   ROLES    AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
worker-0   Ready    <none>   14m     v1.15.0   10.240.0.20   <none>        Ubuntu 18.04.2 LTS   4.15.0-1036-gcp   containerd://1.2.7
worker-1   Ready    <none>   7m7s    v1.15.0   10.240.0.21   <none>        Ubuntu 18.04.2 LTS   4.15.0-1036-gcp   containerd://1.2.7
worker-2   Ready    <none>   2m18s   v1.15.0   10.240.0.22   <none>        Ubuntu 18.04.2 LTS   4.15.0-1036-gcp   containerd://1.2.7
```

Next: [Provisioning Pod Network Routes](11-pod-network-routes.md)
