# Install Kubernetes 1.15 di Centos 7

Terdapat 3 Node yaitu
- Master    (master.i3datacenter.com)
- Worker01  (worker01.i3datacenter.com)
- Worker02  (worker02.i3datacenter.com)

## Set pada semua node
1. set hosts
```
vi /etc/hosts
x.x.x.x master.i3datacenter.com master
x.x.x.x worker01.i3datacenter.com worker01
x.x.x.x worker02.i3datacenter.com worker02
```

2. Disable Selinux dan Firewalld
```
exec bash
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
systemctl disable firewalld
systemctl stop firewalld
systemctl status firewalld
```

3. Setting Value Bridge network di sysctl
```
vi /etc/sysctl.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
```

4. Aktifkan sysctl
```
sysctl -p
sysctl: cannot stat /proc/sys/net/bridge/bridge-nf-call-ip6tables: No such file or directory
sysctl: cannot stat /proc/sys/net/bridge/bridge-nf-call-iptables: No such file or directory
```
!!! jika ada error seperti ini maka anda harus aktifkan modprob br_netfilter

5. Aktfiakan br_netfilter
```
modprobe br_netfilter
sysctl -p
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
```

6. Disable Swap Memory, beri tanda pagar (#) pada swap
```
swapoff  -a
vi /etc/fstab
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=f522e8d8-3430-4f1f-90e5-f580813b121f /boot                   xfs     defaults        0 0
#/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

7. Tambahkan Repository
```
vi /etc/yum.repos.d/kubernetes.repo

[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64

enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
       https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
```

8. Install Kubeadm, Kubectl, kubelet dan kubernetes-cni
```
yum install kubeadm-1.15.1 kubectl-1.15.1 kubelet-1.15.1 kubernetes-cni-0.7.5-0
systemctl restart docker && systemctl enable docker
systemctl restart kubelet && systemctl enable kubelet
```

## Set pada Master node
1. Deploy kubernetes untuk control plane
```
kubeadm init
---dipotong---

our Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join x.x.x.x:6443 --token pjizxk.b9hdimy6nyfpqf8pxxx --discovery-token-ca-cert-hash sha256:f7a77a76f6f83ebb8386401f31d52ce18af22121f3938a763b66ff4ea36d2362
```
2. Tambahkan config kube untuk mengakses cluster k8s
```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Set pada Semua Worker
1. Jalankan kubeadm join - untuk join ke dalam cluster k8s
```
kubeadm join x.x.x.x:6443 --token pjizxk.b9hdimy6nyfpqf8pxxx --discovery-token-ca-cert-hash sha256:f7a77a76f6f83ebb8386401f31d52ce18af22121f3938a763b66ff4ea36d2362
```

