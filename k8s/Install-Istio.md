# Install Istio
Istio adalah aplikasi untuk service mesh di k8s

### Jalankan pada node master
1. Install Istio
```
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.3.2 sh -
cd istio-1.3.2/
cp bin/istioctl /usr/local/bin/
```

2. verify istio
```
# istioctl verify-install
Checking the cluster to make sure it is ready for Istio installation...

#1. Kubernetes-api
-----------------------
Can initialize the Kubernetes client.
Can query the Kubernetes API Server.

#2. Kubernetes-version
-----------------------
Istio is compatible with Kubernetes: v1.15.4.

#3. Istio-existence
-----------------------
Istio will be installed in the istio-system namespace.

#4. Kubernetes-setup
-----------------------
Can create necessary Kubernetes configurations: Namespace,ClusterRole,ClusterRoleBinding,CustomResourceDefinition,Role,ServiceAccount,Service,Deployments,ConfigMap. 

#5. Sidecar-Injector
-----------------------
This Kubernetes cluster supports automatic sidecar injection. To enable automatic sidecar injection see https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/#deploying-an-app

-----------------------
Install Pre-Check passed! The cluster is ready for Istio installation.
```

3. deploy istio
```
# cd /root/istio-1.3.2
# helm install install/kubernetes/helm/istio-init/ --name istio-init --namespace istio-system
# helm list
NAME          	REVISION	UPDATED                 	STATUS  	CHART               	APP VERSION	NAMESPACE   
istio-init    	1       	Mon Oct 14 20:50:43 2019	DEPLOYED	istio-init-1.3.2    	1.3.2      	istio-system
metrics-server	1       	Sun Oct 13 01:36:56 2019	DEPLOYED	metrics-server-2.8.8	0.3.5      	default   

# kubectl get crds | grep 'istio.io' | wc -l
23

# helm install install/kubernetes/helm/istio --name istio --namespace istio-system
```

### Deploy apps bookinfo
1. Create namespaces Bookinfo
```
kubectl create ns bookinfo
```

2. set label inject istio
```
kubectl label namespace bookinfo istio-injection=enabled
```

3. show label namespaces
```
# kubectl get ns --show-labels
NAME                   STATUS   AGE    LABELS
bookinfo               Active   83s    istio-injection=enabled
default                Active   2d2h   <none>
istio-system           Active   33m    name=istio-system
kube-node-lease        Active   2d2h   <none>
kube-public            Active   2d2h   <none>
kube-system            Active   2d2h   <none>
kubernetes-dashboard   Active   47h    <none>
metallb-system         Active   46h    app=metallb
```

4. deploy bookinfo
```
# cd /root/istio-1.3.2
# kubectl create -f  samples/bookinfo/platform/kube/bookinfo.yaml -n bookinfo
# kubectl get pod -n bookinfo
```

5. Create gateway for application bookinfo - Determine the ingress IP and port
```
# kubectl create -f samples/bookinfo/networking/bookinfo-gateway.yaml -n bookinfo
gateway.networking.istio.io/bookinfo-gateway created
virtualservice.networking.istio.io/bookinfo created

# kubectl  get gateway -n bookinfo
NAME               AGE
bookinfo-gateway   18s

# kubectl  describe gateway bookinfo-gateway -n bookinfo
Name:         bookinfo-gateway
Namespace:    bookinfo
Labels:       <none>
Annotations:  <none>
API Version:  networking.istio.io/v1alpha3
Kind:         Gateway
Metadata:
  Creation Timestamp:  2019-10-14T14:37:59Z
  Generation:          1
  Resource Version:    268021
  Self Link:           /apis/networking.istio.io/v1alpha3/namespaces/bookinfo/gateways/bookinfo-gateway
  UID:                 ce4f9245-a607-4b41-917a-1346c8648244
Spec:
  Selector:
    Istio:  ingressgateway
  Servers:
    Hosts:
      *
    Port:
      Name:      http
      Number:    80
      Protocol:  HTTP
Events:          <none>
```
6. access istio-ingressgateway for access application
```
# kubectl  get svc -n istio-system
NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)                                                                                                                                      AGE
istio-citadel            ClusterIP      10.102.214.173   <none>         8060/TCP,15014/TCP                                                                                                                           64m
istio-galley             ClusterIP      10.97.234.127    <none>         443/TCP,15014/TCP,9901/TCP                                                                                                                   64m
istio-ingressgateway     LoadBalancer   10.102.218.198   192.168.1.21   15020:32207/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:32533/TCP,15030:30843/TCP,15031:30842/TCP,15032:31034/TCP,15443:32276/TCP   64m
istio-pilot              ClusterIP      10.99.116.135    <none>         15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       64m
istio-policy             ClusterIP      10.104.106.196   <none>         9091/TCP,15004/TCP,15014/TCP                                                                                                                 64m
istio-sidecar-injector   ClusterIP      10.111.56.32     <none>         443/TCP,15014/TCP                                                                                                                            64m
istio-telemetry          ClusterIP      10.100.13.73     <none>         9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       64m
prometheus               ClusterIP      10.106.123.171   <none>         9090/TCP
```
*note: access to url http://192.168.1.21/productpage, by default istio will be load balance to 3 pod use round robin, so if you refresh web browser the book review will be change to v1 or v2 or v3.

