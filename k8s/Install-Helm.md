# Install Helm
Helm adalah package manager di kubernetes. pada helm terdapat 2 komponen yaitu:
- Helm Client
- Helm Server (Tiller)

1. Install Helm Client
```
# curl -L https://git.io/get_helm.sh | bash
```

2. Install Helm Server (Tiller)
Untuk bisa menginstall tiller kita harus membuat serviceAccount dan ClusterRoleBinding, dimana pada ClusterRoleBinding akan memberikan permission access ke ServiceAccount
```
# kubectl create -f k8s/k8s-helm/helm-rbac.yaml
# helm init --service-account=tiller
$HELM_HOME has been configured at /root/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
To prevent this, run `helm init` with the --tiller-tls-verify flag.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation

# kubectl  get pod -n kube-system
NAME                                              READY   STATUS    RESTARTS   AGE
coredns-5c98db65d4-29mkn                          1/1     Running   0          6h38m
coredns-5c98db65d4-9hqhv                          1/1     Running   0          6h38m
etcd-master.i3datacenter.com                      1/1     Running   0          6h38m
kube-apiserver-master.i3datacenter.com            1/1     Running   0          6h38m
kube-controller-manager-master.i3datacenter.com   1/1     Running   0          6h37m
kube-proxy-mqtgc                                  1/1     Running   0          6h38m
kube-proxy-nbsl8                                  1/1     Running   0          6h37m
kube-proxy-nmjb5                                  1/1     Running   0          6h37m
kube-scheduler-master.i3datacenter.com            1/1     Running   0          6h37m
tiller-deploy-8557598fbc-tbfj6                    1/1     Running   0          116s
weave-net-49vb6                                   2/2     Running   0          6h36m
weave-net-qxd4j                                   2/2     Running   0          6h36m
weave-net-zhp2d                                   2/2     Running   0          6h36m
```
*note: pastikan pod tiller-deploy statusnya "Running"

3. Testing Helm
```
helm list
```
*note: pastikan tidak ada error

4. Cek versi helm
```
# helm version --short
Client: v2.14.3+g0e7f3b6
Server: v2.14.3+g0e7f3b6
```