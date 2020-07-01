---
layout: pagelt
title: nginx-ingress 安装配置指定多节点访问及Service设置
tags: [experience]
---

#私有集群默认安装
helm install --name nginx-ingress stable/nginx-ingress \
    --set rbac.create=true,controller.hostNetwork=true,controller.dnsPolicy=ClusterFirstWithHostNet

#配置多节点访问ingress：
kubectl label nodes fingerprint ingress-controller-ready=true
kubectl label nodes vs1 ingress-controller-ready=true
helm install --name nginx-ingress stable/nginx-ingress \
    --set rbac.create=true,controller.kind=DaemonSet,controller.hostNetwork=true,controller.nodeSelector=ingress-controller-ready:true

如果不设置rbac.create=true，则pod/nginx-ingress-controller无法启动，状态crashbackoff;
如果不设置controller.hostNetwork=true，则外部无法通过node IP访问；
只能通过pod/nginx-ingress-controller所在node IP访问，不能设置externalIPs, 否则引起节点连接异常；
kubectl patch svc nginx-ingress-controller -p '{"spec":{"externalIPs":["10.6.78.242"]}}'
后节点10.6.78.246无法连接，在242上无法ping或ssh连接246了；

默认安装在private cluster(using kubeamd)时，因为缺少Loadbalance服务，nginx-ingress-controller服务状态为pending，且无法获取External-IP，可通过给Service/istio-ingressgateway设置externalIPs完成配置：
```bash
kubectl edit svc istio-ingressgateway
#add: be very caution to add externalIPs,this may cause network errors
externalIPs:
  - 10.6.78.242
```
docker for desktop中nginx-ingress-controller的配置方式是：externalTrafficPolicy: Local，type: LoadBalancer

参考：
<https://github.com/kubernetes/ingress-nginx>
<https://kubernetes.github.io/ingress-nginx/>
<https://hub.helm.sh/charts/stable/nginx-ingress>
[深入玩转K8S之外网如何访问业务应用（nginx-ingress篇）](https://blog.51cto.com/devingeng/2149377)
[为私有Kubernetes集群创建LoadBalancer服务](https://my.oschina.net/u/2306127/blog/1925933)