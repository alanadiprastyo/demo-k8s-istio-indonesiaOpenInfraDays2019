# Install Kubernetes Dashboard
### Jalankan pada node Master
1. Install Kubernetes Dashboard
```
kubectl create -f k8s/k8s-dashboard/kubernetes-dashboard.yaml
```

2. cek pod  kubernetes dashboard
```
# kubectl get pod -n kubernetes-dashboard
NAME                                        READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-fb986f88d-4vnjm   1/1     Running   0          24m
kubernetes-dashboard-6bb65fcc49-q585r       1/1     Running   0          24m
```
*note: pastikan pod kubernetes-dashboard 'Running'

3. Create Rolebinding dan Serviceaccount  
```
# kubectl create -f k8s/k8s-dashboard/role-k8s-dashboard.yaml
# kubectl create -f k8s/k8s-dashboard/sa-admin.yaml
```

4. decribe serviceAccount admin-user
```
# kubectl describe sa admin-user -n kubernetes-dashboard
Name:                admin-user
Namespace:           kubernetes-dashboard
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   admin-user-token-lhpbg     ---> copy bagian ini
Tokens:              admin-user-token-lhpbg
Events:              <none>
```

5. Describe secret token admin-user-token
```
# kubectl  describe secret admin-user-token-lhpbg -n kubernetes-dashboard
Name:         admin-user-token-lhpbg
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: 93e7d746-5112-4c21-875d-171c86769961

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWxocGJnIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI5M2U3ZDc0Ni01MTEyLTRjMjEtODc1ZC0xNzFjODY3Njk5NjEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.su07GdcCJuDZlcDkxsaSpOiQiGdrAbypVq3DMmzKrc-YR9ZB3qQAWr9SggnWmf_D332090KS-NIAhf3oLWgulmC0aeL9laGILXV3yBF7iNNuI2CnpvFt0Z83q6U8aQcN3jBtnbc3hqxMo67dbzscxoasq55s8tUDo1rqSeyjZGgc4DqLWmm5emle5Wpu_8XkVTgj7_LEPj0R_Yp4PVcjcxtW5uhgZ67S4r5MW8D-tI9vbDtHJoBHFg-H7kHLSyAgfu2EktPbbkpbRnuGSUUFCTiX3nphOngec5QuhzS9d8dAGLVBMBk8z0T0WBwlehnH3aTv7lkQijbzCikOg-J-qw
```
- note: copy bagian token, kemudian paste pada akses diweb browser pada saat akses dashboard-kubernetes
- note: Halaman url (https://x.x.x.x:30323)