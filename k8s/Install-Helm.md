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

5. Install Metric k8s
```
# helm install stable/metrics-server --set rbac.create=true --set args[0]="--kubelet-insecure-tls=true" --set args[1]="--kubelet-preferred-address-types=InternalIP" --set args[2]="--v=2" --name metrics-server

# helm list
NAME          	REVISION	UPDATED                 	STATUS  	CHART               	APP VERSION	NAMESPACE
metrics-server	1       	Sun Oct 13 01:36:56 2019	DEPLOYED	metrics-server-2.8.8	0.3.5      	default  

# kubectl top nodes
NAME                        CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
master.i3datacenter.com     262m         13%    1442Mi          39%       
worker01.i3datacenter.com   76m          7%     729Mi           41%       
worker02.i3datacenter.com   32m          3%     956Mi           55%   

# kubectl -n kube-system top pod
NAME                                              CPU(cores)   MEMORY(bytes)   
coredns-5c98db65d4-29mkn                          2m           9Mi             
coredns-5c98db65d4-9hqhv                          2m           9Mi             
etcd-master.i3datacenter.com                      43m          73Mi            
kube-apiserver-master.i3datacenter.com            63m          341Mi           
kube-controller-manager-master.i3datacenter.com   15m          48Mi            
kube-proxy-mqtgc                                  6m           12Mi            
kube-proxy-nbsl8                                  1m           12Mi            
kube-proxy-nmjb5                                  5m           11Mi            
kube-scheduler-master.i3datacenter.com            3m           14Mi            
tiller-deploy-8557598fbc-tbfj6                    1m           11Mi            
weave-net-49vb6                                   1m           76Mi            
weave-net-qxd4j                                   2m           74Mi            
weave-net-zhp2d                                   1m           88Mi  
```