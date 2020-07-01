---
layout: pagelt
title: Kubernetes cluster install with kubeadm
tags: [experience]
---

(master on 10.6.78.242, node on 10.6.78.246)
ssh proxy:
ssh -R 8100:10.9.26.13:8080 root@10.6.78.246
export http_proxy=http://127.0.0.1:8100 && export https_proxy=http://127.0.0.1:8100 && export no_proxy=127.0.0.1,localhost,10.96.0.0/12,10.6.78.246,10.6.78.242,10.0.0.0/24
or ssh root@10.6.78.246

install docker-ce:
`curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun`

启动docker service:
```bash
sudo service docker start
# or 设置开机自启：systemctl enable docker&& systemctl start docker
docker version
# set proxy for docker
mkdir /etc/systemd/system/docker.service.d
vim  /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://wang.weili:wwl@3345@10.9.26.13:8080" "NO_PROXY=localhost,127.0.0.1,10.96.0.0/12,10.6.78.246,10.6.78.242"
# 配置docker镜像仓库
cat > /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
EOF
# 可选：配置并确保docker cgroupdriver与kubelet一致，kubernetes官网建议使用systemd vs cgroupfs
cat > /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://registry.docker-cn.com"],
  "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF
sudo systemctl daemon-reload
systemctl restart docker
# 如果代理配置成功，此命令查看环境配置
systemctl show --property=Environment docker
# 查看mirrors
docker info
# 验证proxy和registry
docker search alpine
```

#可选 install Docker Compose
```
curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

install kubernetes:
```bash

# 设置ali kubernetes repo
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# 关闭firewalld/Selinux
systemctl stop firewalld
systemctl disable firewalld
setenforce 0
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config

#设置时间
timedatectl
timedatectl set-timezone Asia/Shanghai
##可选，修正时间
timedatectl set-time "YYYY-MM-DD HH:MM:SS"

# 设置host
hostname
hostnamectl --static set-hostname fingerprint
#两台均加入hosts
cat /etc/hosts
10.6.78.264 vs1
10.6.78.242 fingerprint
# 设置no-proxy，避免加入时通过代理连接10.6.78.246
export no_proxy=127.0.0.1,localhost,10.6.78.246,10.6.78.242,10.96.0.0/12

# 关闭交换分区
swapoff -a
yes | cp /etc/fstab /etc/fstab_bak
cat /etc/fstab_bak |grep -v swap > /etc/fstab
# 确认交换分区关闭
free -m

# 设置网桥包经IPTables，core文件生成路径
echo """
vm.swappiness = 0
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
""" > /etc/sysctl.conf
sysctl -p

# master节点安装kubelet kubeadm
yum install -y kubelet kubeadm
systemctl enable kubelet && systemctl start kubelet

# 拉取镜像并初始化
kubeadm config print init-defaults > kubeadm.conf
vi kubeadm.conf
# 修改 imageRepository: k8s.gcr.io为azure mirrors
imageRepository: gcr.azk8s.cn/google_containers
# 修改 advertiseAddress:1.2.3.4为10.6.78.242或0.0.0.0
advertiseAddress: 10.6.78.242
# 此配置Weave Net作为pod network addon，可选用其他addon则可能还需要[参考](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network)修改配置
# 查看kubeadm config所需的镜像
kubeadm config images list --config kubeadm.conf
# 拉取kubeadm config所需的镜像
kubeadm config images pull --config kubeadm.conf
systemctl enable kubelet.service
# 初始化
kubeadm init --config kubeadm.conf
#Your Kubernetes control-plane has initialized successfully!
#
#To start using your cluster, you need to run the following as a regular user:
#To make kubectl work for your non-root user, run these commands, which are also part of the kubeadm init output:
# mkdir -p $HOME/.kube
#  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
#  sudo chown $(id -u):$(id -g) $HOME/.kube/config
#
#You should now deploy a pod network to the cluster.
#Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
#  https://kubernetes.io/docs/concepts/cluster-administration/addons/
#
#Then you can join any number of worker nodes by running the following on each as root:
#
#kubeadm join 10.6.78.246:6443 --token abcdef.0123456789abcdef \
#    --discovery-token-ca-cert-hash sha256:269b252509c2aca7a42789d7f6ba9a9cec9c51961e984a3a5c5a7d093be987d6
# Alternatively, if you are the root user, you can run:
export KUBECONFIG=/etc/kubernetes/admin.conf
kubectl get cs
# install network addon: Weave Net
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
# 确保所有的Pod都处于Running状态
kubectl get pod –all-namespaces -o wide
# 默认master node不参与工作负载
kubectl describe node fingerprint | grep Taint
# master node参与工作负载
kubectl taint nodes fingerprint node-role.kubernetes.io/master-
```

可选：升级内核&&开启IPVS
```bash
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4
# 上面脚本创建了的/etc/sysconfig/modules/ipvs.modules文件，保证在节点重启后能自动加载所需模块
# 查看是否已经正确加载所需的内核模块
lsmod | grep -e ip_vs -e nf_conntrack_ipv4
# 安装了ipset软件包
yum install ipset
# 可选：安装ipvsadm
yum install ipvsadm
reboot

# kube-proxy开启ipvs
# 修改ConfigMap的kube-system/kube-proxy中的config.conf，mode: “ipvs”：
kubectl edit cm kube-proxy -n kube-system
# 之后重启各个节点上的kube-proxy pod：
kubectl get pod -n kube-system | grep kube-proxy | awk '{system("kubectl delete pod "$1" -n kube-system")}'
# 确认日志中打印出了Using ipvs Proxier，说明ipvs模式已经开启
kubectl get pod -n kube-system | grep kube-proxy
kubectl logs kube-proxy-qd2ff -n kube-system
```

# 节点加入集群
```bash
kubeadm join 10.6.78.246:6443 --token abcdef.0123456789abcdef \
  --discovery-token-ca-cert-hash sha256:269b252509c2aca7a42789d7f6ba9a9cec9c51961e984a3a5c5a7d093be987d6
# or 
kubeadm join 10.6.78.246:6443 --discovery-token-unsafe-skip-ca-verification --token=abcdef.0123456789abcdef
# 如果遇到错误，添加 -v=6查看日志： kubeadm join 10.6.78.246:6443 -v=6
# 默认token的有效期为24小时，当过期之后，该token就不可用了，解决办法如下：
## 重新生成token
kubeadm token create
kubeadm token list
##获取ca证书sha256编码hash值
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
## 节点加入集群
kubeadm join 10.6.78.242:6443 --token o7htqx.opp9wx8mfmv88h86 --discovery-token-ca-cert-hash sha256:5e40d03bfbb0d349435efa383d8683097a37aed6a14dc144043765f90551a137
```

# 移除节点(not test)
```bash
# run on master
kubectl drain node2 --delete-local-data --force --ignore-daemonsets
# run on node
kubeadm reset
# run on master
kubectl delete node node2
```

# kubeadm 管理集群：ref: <https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/kubeadm-init/#config-file>
##kubeadm upgrade 将 Kubernetes 集群升级到新版本，谨慎操作，不确定是否引起deployment无法新建pod的原因
kubeadm upgrade plan [version]
kubeadm upgrade apply [version] --config /home/kubeadm.conf
# get config
kubectl -n kube-system get cm kubeadm-config -o yaml
##kubeadm reset 使用 kubeadm init 或者 kubeadm join来恢复对节点的改变
kubectl drain vs1 --delete-local-data --force --ignore-daemonsets
kubectl delete node vs1
#running on node
kubeadm reset
ipvsadm -C

# 查看节点kubelet日志
journalctl -f -u kubelet


参考：
[使用kubeadm安装Kubernetes 1.13](https://www.kubernetes.org.cn/4956.html)
[Creating Highly Available Clusters with kubeadm](https://kubernetes.io/docs/setup/independent/high-availability/)
[IPVS](https://github.com/kubernetes/kubernetes/blob/master/pkg/proxy/ipvs/README.md)
[cgroups](https://kubernetes.io/docs/setup/cri/)
[Docker中的Cgroup Driver:Cgroupfs 与 Systemd](https://www.cnblogs.com/hongdada/p/9771857.html)
[kubeadm init从私有仓库拉取镜像](https://www.jianshu.com/p/d42ef0eff63f)
[在你的集群中使用内网 IP](https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/kubeadm-init/#%E5%9C%A8%E4%BD%A0%E7%9A%84%E9%9B%86%E7%BE%A4%E4%B8%AD%E4%BD%BF%E7%94%A8%E5%86%85%E7%BD%91-ip)
[在没有互联网连接的情况下运行 kubeadm](https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/kubeadm-init/#%E5%9C%A8%E6%B2%A1%E6%9C%89%E4%BA%92%E8%81%94%E7%BD%91%E8%BF%9E%E6%8E%A5%E7%9A%84%E6%83%85%E5%86%B5%E4%B8%8B%E8%BF%90%E8%A1%8C-kubeadm)

googlecontainers镜像仓库：
1. [docker hub mirrors](https://hub.docker.com/u/mirrorgooglecontainers)
2. [azure GCR Proxy](http://mirror.azure.cn/help/gcr-proxy-cache.html)
3. [阿里巴巴开源镜像站](https://opsx.alibaba.com/mirror)