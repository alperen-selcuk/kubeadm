# kubeadm ile cluster kurulumu.

## all nodes

### containerd

sudo su -
apt-get update
apt-get install containerd -y
mkdir -p /etc/containerd
containerd config default /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl enable containerd

### sys

sudo modprobe overlay
sudo modprobe br_netfilter

sudo nano /etc/sysctl.d/kubernetes.conf

net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1

### kubeadm, kubelet, kubectl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add

sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

apt-cache madison kubeadm // mevcut versiyonları görme.

apt-get install kubeadm=1.24.2-00 kubelet=1.24.2-00 kubectl=1.24.2-00 -y

### kubeadm ile upgrade

apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=1.25.2-00 && \
apt-mark hold kubeadm
 
kubeadm upgrade plan

sudo kubeadm upgrade apply v1.25.2


kubectl drain master --ignore-daemonsets

apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.25.2-00 kubectl=1.25.2-00 && \
apt-mark hold kubelet kubectl

sudo systemctl daemon-reload
sudo systemctl restart kubelet

kubectl uncordon master

