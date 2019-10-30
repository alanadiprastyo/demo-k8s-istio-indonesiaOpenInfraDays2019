# Demo Istio 
 1. Clone Repository
 ```
 git clone https://github.com/alanadiprastyo/demo-k8s-istio-indonesiaOpenInfraDays2019.git
 cd /root/demo-k8s-istio-indonesiaOpenInfraDays2019/k8s/istio-demo
```

2. Membuat Namespace
```
kubectl create ns mylab
```

3. Menambahkan label istio pada namespaces mylab
```
kubectl label namespace mylab istio-injection=enabled
```

4. Jalankan semua deployment
```
kubectl create -f . -n mylab
```

5. Tampilkan semua pod
```
# kubectl get pod -n mylab
NAME                         READY   STATUS    RESTARTS   AGE
mydata-v1-68b74657bb-g6fgt   2/2     Running   0          49s
mydata-v2-fbf7667c6-49kkl    2/2     Running   0          49s
myweb-6d9ccff74-lxt7x        2/2     Running   0          49s
```

6. menampilkan external IP
```
kubectl -n istio-system get svc istio-ingressgateway
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)                                                                                    AGE
istio-ingressgateway   LoadBalancer   10.105.254.230   192.168.1.22   15020:31657/TCP,80:31380/TCP,443:31390/TCP,15031:30000/TCP,15032:31148/TCP,15443:30550/TCP   11d
```
note: Loadbalancer IP => 192.168.1.22

7. Merubah hosts
```
vi /etc/hosts
192.168.1.22 myweb.routecloud.net
```

8. Looping akses ke myweb.routecloud.net
```
while true; do curl -s -o /dev/null myweb.routecloud.net; sleep 1; done &
```

9. Akses Istio - Kiali Dashboard
```
http://192.168.1.24:20001
```

10. Manipulasi Routing
```
Appplication -> mydata -> Created weight routing
```

