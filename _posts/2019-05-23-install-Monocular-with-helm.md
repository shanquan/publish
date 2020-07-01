---
layout: pagelt
title: helm 安装Monocular
tags: [experience]
---

#安装Nginx Ingress controller
helm install --name nginx-ingress stable/nginx-ingress --set rbac.create=true

#安装Monocular
helm repo add monocular https://helm.github.io/monocular
#Configuring chart repositories && charts sync proxy
cat > custom-repos.yaml <<EOF
sync:
  repos:
    - name: stable
      url: https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
      schedule: "0 * * * *"
      successfulJobsHistoryLimit: 1
    - name: incubator
      url: https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts-incubator
      schedule: "*/5 * * * *"
   httpProxy: "http://wang.weili:wwl%403345@10.9.26.13:8080"
   httpsProxy: "http://wang.weili:wwl%403345@10.9.26.13:8080"
   noProxy: "127.0.0.1,localhost,10.0.0.0/24"
EOF
# `schedule` and `successfulJobsHistoryLimit` are optional parameters. They default to `"0 * * * *"` and `3` respectively
helm install monocular/monocular  -f custom-repos.yaml
#可选：monocular-ui镜像quay.io/helmpack/monocular-ui:v1.6.0无法拉取问题处理
docker pull quay.io/helmpack/monocular-ui:v1.6.0

# monocular-ui ingress遇到问题：可以解析service,但是无法解析js/css资源。在pod network中通过ip可以解析js等资源文件
# 查看nginx-ingress log，发现nginx-ingress会自动给path添加/(.*)，自动给rewrite添加/$1，在monocular中需要进行修改
kubectl logs -f dozing-catfish-nginx-ingress-controller-55b5ffdff4-lwvsp
# 修改monocular ingress配置path和annotations:nginx.ingress.kubernetes.io/rewrite-target
kubectl edit ingress lumbering-whale-monocular
# nginx.ingress.kubernetes.io/rewrite-target: /$1改为nginx.ingress.kubernetes.io/rewrite-target: /
# path: /?(.*) 改为 path: / ； path: /api/chartsvc/?(.*) 改为：path: /api/chartsvc

#Access Monocular
kubectl get ingress
# visit http://$(ADDRESS)

# 可选：monocular无法同步charts，如果安装时候未设置代理的话，pod无法访问外网
##查看monocular 同步charts的cronjob，并设置http代理环境变量
kubectl get cronjob
## 可选：删除cronjob
kubectl delete cronjob/lumbering-whale-monocular-sync-scheduled-monocular
kubectl edit cronjob/lumbering-whale-monocular-sync-scheduled-incubator
kubectl edit cronjob/lumbering-whale-monocular-sync-scheduled-stable
#同步时间改为每天12点执行一次
# schedule: '0 0 12 * *'
# env:
#  - name: HTTP_PROXY
#    value: "http://wang.weili:wwl%403345@10.9.26.13:8080"
#  - name: HTTPS_PROXY
#    value: "http://wang.weili:wwl%403345@10.9.26.13:8080"
#  - name: NO_PROXY
#     value: "127.0.0.1,localhost,10.0.0.0/24"
#查看确认job是否完成
kubectl get job

参考：
<https://kubernetes.github.io/ingress-nginx/user-guide/tls/#http-strict-transport-security>
[http自动转https](https://github.com/kubernetes/ingress-nginx/blob/master/docs/examples/rewrite/README.md)