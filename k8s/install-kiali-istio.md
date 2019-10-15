# Install Kiali Istio
Kiali is project opensource for visualization you apps service in kubernetes, before of this you already have grafana to monitoring you apps, but in this section i will show you to install kiali in k8s for monitoring an tracing you apps use kiali+jaeger https://istio.io/docs/tasks/telemetry/kiali/

1. Create a Credential
```
# KIALI_USERNAME=$(read -p 'Kiali Username: ' uval && echo -n $uval | base64)
# KIALI_PASSPHRASE=$(read -sp 'Kiali Passphrase: ' pval && echo -n $pval | base64)
```

2. Create secret
```
# NAMESPACE=istio-system
# cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: $NAMESPACE
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF

# kubectl describe kiali secret -n istio-system 
Name:         kiali
Namespace:    istio-system
Labels:       app=kiali
Annotations:  
Type:         Opaque

Data
====
passphrase:  5 bytes
username:    5 bytes

```

3. helm upgrade istio use kiali 
```
cd /root/istio-1.3.2
# helm list
NAME          	REVISION	UPDATED                 	STATUS  	CHART               	APP VERSION	NAMESPACE   
istio         	2       	Mon Oct 14 22:23:03 2019	DEPLOYED	istio-1.3.2         	1.3.2      	istio-system
istio-init    	1       	Mon Oct 14 20:50:43 2019	DEPLOYED	istio-init-1.3.2    	1.3.2      	istio-system
metrics-server	1       	Sun Oct 13 01:36:56 2019	DEPLOYED	metrics-server-2.8.8	0.3.5      	default 

# helm upgrade istio install/kubernetes/helm/istio --set kiali.enabled=True
# kubectl get pod -n istio-system
NAME                                      READY   STATUS      RESTARTS   AGE
istio-citadel-658567496c-nz4sk            1/1     Running     1          138m
istio-galley-9695fbd77-45tr8              1/1     Running     1          138m
istio-ingressgateway-5dbbc9dcb8-cdv8d     1/1     Running     1          138m
istio-init-crd-10-1.3.2-z6m7f             0/1     Completed   0          145m
istio-init-crd-11-1.3.2-ltbbd             0/1     Completed   0          145m
istio-init-crd-12-1.3.2-nbzcf             0/1     Completed   0          145m
istio-pilot-654c95d47b-5tv9l              2/2     Running     0          138m
istio-policy-8cdf94ffd-vr4b6              2/2     Running     4          138m
istio-sidecar-injector-54d9c5b6f8-skqm4   1/1     Running     1          138m
istio-telemetry-6c489bf6bc-jcvfg          2/2     Running     0          138m
kiali-8c9d6fbf6-b8hqz                     1/1     Running     0          19s
prometheus-7d7b9f7844-nx24r               1/1     Running     1          138m
```
*note: make sure the pod kiali running well

4. create destionatio rule istio
```
cd /root/istio-1.3.2
# kubectl create -f samples/bookinfo/networking/destination-rule-all.yaml -n bookinfo
destinationrule.networking.istio.io/productpage created
destinationrule.networking.istio.io/reviews created
destinationrule.networking.istio.io/ratings created
destinationrule.networking.istio.io/details created
```

5. change service kiali from ClusterIP to NodePort
```
# kubectl edit svc kiali -n istio-system
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2019-10-14T16:16:00Z"
  labels:
    app: kiali
    chart: kiali
    heritage: Tiller
    release: istio
  name: kiali
  namespace: istio-system
  resourceVersion: "276782"
  selfLink: /api/v1/namespaces/istio-system/services/kiali
  uid: 8dffb779-e074-4de9-9edb-90340fd1359c
spec:
  clusterIP: 10.106.251.73
  ports:
  - name: http-kiali
    port: 20001
    protocol: TCP
    targetPort: 20001
  selector:
    app: kiali
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}

#  kubectl get svc kiali -n istio-system
NAME    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
kiali   NodePort   10.106.251.73   <none>        20001:31349/TCP   7m15s
```
*note:  access  to kiali you can access http://192.168.1.174:31349
```
watch curl -s -o /dev/null 192.168.1.21/productpage
```