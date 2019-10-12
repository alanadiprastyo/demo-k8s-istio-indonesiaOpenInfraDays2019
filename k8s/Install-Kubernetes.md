# Install Kubernetes 1.15 di Centos 7

Terdapat 3 Node yaitu
- Master    (master.i3datacenter.com)
- Worker01  (worker01.i3datacenter.com)
- Worker02  (worker02.i3datacenter.com)

## Set pada semua node
1. set hosts
    ``vi /etc/hosts
    x.x.x.x master.i3datacenter.com master
    x.x.x.x worker01.i3datacenter.com worker01
    x.x.x.x worker02.i3datacenter.com worker02

2. Disable Selinux dan Firewalld

