---
layout: pagelt
title: 安装Helm
tags: [experience]
---

```bash
curl -OL https://storage.googleapis.com/kubernetes-helm/helm-v2.13.1-linux-amd64.tar.gz
# 可选：本地下载后上传至服务器
scp  -r ./helm-v2.13.1-linux-amd64.tar.gz  root@10.6.78.246:/home
tar -zxvf helm-v2.13.1-linux-amd64.tar.gz
cd linux-amd64/
cp helm /usr/local/bin/
chmod +x /usr/local/bin/helm
helm help

#创建tiller使用的service account
cat << EOF >  rbac-config.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
EOF

kubectl create -f rbac-config.yaml
# 查看client 版本，并使之替换tiller:version
helm version
helm init --service-account tiller --upgrade -i registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:v2.13.1  --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
# 可选：添加incubator repo
helm repo add incubator https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts-incubator/
# 查看tiller安装
kubectl get pod -n kube-system -l app=helm
helm version

# helm使用
helm search
helm repo update
# 查看已安装charts
helm list
helm del --purge ${name}

参考：
- [kubernetes之helm简介、安装、配置、使用指南](https://blog.csdn.net/bbwangj/article/details/81087911)