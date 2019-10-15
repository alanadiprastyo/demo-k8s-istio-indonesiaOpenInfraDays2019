# Install Grafana in Istio
in this case, we already have istio but not with grafana for moniting apps, so i will upgrade the deployment helm use existing repo with upgrade the deployment before

1. Upgrade istio with grafana
```
cd /root/istio-1.3.2
# helm list
NAME          	REVISION	UPDATED                 	STATUS  	CHART               	APP VERSION	NAMESPACE   
istio         	2       	Mon Oct 14 22:23:03 2019	DEPLOYED	istio-1.3.2         	1.3.2      	istio-system
istio-init    	1       	Mon Oct 14 20:50:43 2019	DEPLOYED	istio-init-1.3.2    	1.3.2      	istio-system
metrics-server	1       	Sun Oct 13 01:36:56 2019	DEPLOYED	metrics-server-2.8.8	0.3.5      	default 

# helm upgrade istio install/kubernetes/helm/istio --set grafana.enabled=True
# kubectl get pod -n istio-system
NAME                                      READY   STATUS      RESTARTS   AGE
grafana-59d57c5c56-x5lpn                  1/1     Running     0          3m50s
istio-citadel-658567496c-nz4sk            1/1     Running     1          88m
istio-galley-9695fbd77-45tr8              1/1     Running     1          88m
istio-ingressgateway-5dbbc9dcb8-cdv8d     1/1     Running     1          88m
istio-init-crd-10-1.3.2-z6m7f             0/1     Completed   0          96m
istio-init-crd-11-1.3.2-ltbbd             0/1     Completed   0          96m
istio-init-crd-12-1.3.2-nbzcf             0/1     Completed   0          96m
istio-pilot-654c95d47b-5tv9l              2/2     Running     0          88m
istio-policy-8cdf94ffd-vr4b6              2/2     Running     4          88m
istio-sidecar-injector-54d9c5b6f8-skqm4   1/1     Running     1          88m
istio-telemetry-6c489bf6bc-jcvfg          2/2     Running     0          88m
prometheus-7d7b9f7844-nx24r               1/1     Running     1          88m
```
*note: we have pod grafana in the namespace istio-system after upgrade the deployment istio use helm

2. change service grafana to NodePort
```
# kubectl  get svc -n istio-system grafana
NAME      TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
grafana   ClusterIP   10.97.54.117   <none>        3000/TCP   15m

# kubectl  edit svc -n istio-system grafana
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2019-10-14T15:22:58Z"
  labels:
    app: grafana
    chart: grafana
    heritage: Tiller
    release: istio
  name: grafana
  namespace: istio-system
  resourceVersion: "272051"
  selfLink: /api/v1/namespaces/istio-system/services/grafana
  uid: 284cfef2-ded7-4c6f-83a5-530b19b4d49c
spec:
  clusterIP: 10.97.54.117
  ports:
  - name: http
    port: 3000
    protocol: TCP
    targetPort: 3000
  selector:
    app: grafana
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}

# kubectl  get svc -n istio-system grafana
NAME      TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
grafana   NodePort   10.97.54.117   <none>        3000:31814/TCP   17m
```
*note: access grafana in web browser use NodePort 31814 (http://192.168.1.174:31814)

3. show istio workload dashboard
```
watch curl -s -o /dev/null 192.168.1.21/productpage
```
*note: the monitoring grafana in istio workload dashboard or istio service dashboard is real time, you must test direct access with curl, and this script will be running every second to test the connection to apps bookinfo, and the you can see in the grafana to monitoring your apps, and you can see the apps running well or not.

