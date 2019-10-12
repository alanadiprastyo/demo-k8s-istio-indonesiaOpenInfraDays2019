# Install MetalLB
MetalLB adalah layanan yang verfungsi sebagai network load balancer di kubernetes baremetal. 
URL => https://metallb.universe.tf/installation/

## jalankan ini pada node Master
1. Install MetalLB
```
# kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.8.1/manifests/metallb.yaml
namespace/metallb-system created
podsecuritypolicy.policy/speaker created
serviceaccount/controller created
serviceaccount/speaker created
clusterrole.rbac.authorization.k8s.io/metallb-system:controller created
clusterrole.rbac.authorization.k8s.io/metallb-system:speaker created
role.rbac.authorization.k8s.io/config-watcher created
clusterrolebinding.rbac.authorization.k8s.io/metallb-system:controller created
clusterrolebinding.rbac.authorization.k8s.io/metallb-system:speaker created
rolebinding.rbac.authorization.k8s.io/config-watcher created
daemonset.apps/speaker created
deployment.apps/controller created
```

2. Cek pod metalLb
```
# kubectl  get pod -n metallb-system
NAME                        READY   STATUS    RESTARTS   AGE
controller-55d74449-qndmx   1/1     Running   0          9m48s
speaker-9nszg               1/1     Running   0          9m48s
speaker-q4lb5               1/1     Running   0          9m48s
speaker-qcd4d               1/1     Running   0          9m48s
```
*note: pastikan semua pod pada namespaces metallb-system 'Running'

3. membuat configMap untuk Pool IP Load balancer
```
# kubectl create -f k8s/ConfigMap-PoolIP-LB.yaml 
configmap/config created
```

4. Describe Configmap config pada namespace metallb-system
```
# kubectl describe  cm config -n metallb-system
Name:         config
Namespace:    metallb-system
Labels:       <none>
Annotations:  <none>

Data
====
config:
----
address-pools:
- name: default
  protocol: layer2
  addresses:
  - 192.168.1.21-192.168.1.40

```

5. Test Deploy Aplikasi
```
# kubectl create ns projectku
# kubectl run nginx --image nginx -n projectku
# kubectl get all -n projectku
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-7bb7cd8db5-zrf2f   1/1     Running   0          13s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           13s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-7bb7cd8db5   1         1         1       13s

# kubectl expose deploy nginx --port 80 --type LoadBalancer -n projectku
# kubectl  get svc -n projectku
NAME    TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)        AGE
nginx   LoadBalancer   10.104.27.151   192.168.1.21   80:30739/TCP   19s
```

6. Akses aplikasi nginx
```
# curl 192.168.1.21
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```