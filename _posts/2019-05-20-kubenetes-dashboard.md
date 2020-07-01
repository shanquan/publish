---
layout: pagelt
title: 部署 kubernetes dashboard
tags: [experience]
---

curl -o https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
# 修改image: registry.cn-hangzhou.aliyuncs.com/google_containers/kubernetes-dashboard-amd64:v1.10.1
kubectl apply -f kubernetes-dashboard.yaml
#or see local as blow 
kubectl proxy
# access address can only be 127.0.0.1 or localhost, and not support 10.6.78.246
## 可选：指定所有地址或端口
kubectl proxy --address 0.0.0.0 --accept-hosts '.*' -p 8080
<http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/>

# 配置ingress 通过域名k8s.byd.com访问dashboard
##生成证书, kubernetes CA查看ls /etc/kubernetes/pki
mkdir /home/certs && cd /home/certs
openssl genrsa -out tls.key 2048
openssl req -new -x509 -key tls.key -out tls.crt -subj /O=BYD Co/CN=k8s.byd.com
# or使用以下命令生成证书
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=k8s.byd.com/O=BYD Co"
# 生成两个文件： 
ls
tls.crt  tls.key
#创建secret
kubectl -n kube-system  create secret tls dashboard-ingress-secret --key tls.key --cert tls.crt
kubectl get secret -n kube-system | grep ingress
#创建ingress
cat << EOF > dashboard-ingress.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: kube-system
  annotations:
    nginx.ingress.kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/secure-backends: "true"
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
spec:
  tls:
  - hosts:
    - k8s.byd.com
    secretName: dashboard-ingress-secret
  rules:
    - host: k8s.byd.com
      http:
        paths:
        - path: /
          backend:
            serviceName: kubernetes-dashboard
            servicePort: 443
EOF
kubectl create -f dashboard-ingress.yaml
# 配置hosts解析，添加 10.6.78.242 k8s.byd.com，然后浏览器访问
<https://k8s.byd.com>
# use curl
curl -H 'Host: k8s.byd.com' 10.6.78.242
curl -I -HHost:k8s.byd.com 10.6.78.242

# 通过default Token访问dashboard
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep tiller | awk '{print $1}')
# copy token from the output and paster in loginForm
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJ0aWxsZXItdG9rZW4tbGg1OXYiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoidGlsbGVyIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiNmU5ZDViZGEtNzdiNy0xMWU5LWJiYzAtYTAzNjlmZDRiMzg0Iiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOnRpbGxlciJ9.umKk-fkQ1jBwbgJxfSSchIgKOTLlUQM_rvA2XhhodQmS9lVzLTvfRBCvjvrI4L2FRMlspa7HvjdhZ9hV65YNvcb5WZKYxVESsH9ilqjkBSG6yFZIstRniWYHpPpBYp1MNVS2uOXaWxnfQBS_RIGzWVjZ_o9C3-6eMAWiDJ8CBrgXrc_keAb7a5HWwgC_Gw2_PSVqcGeMpEaVr2IoHuqML23EBwDYNn68ESUDL1LbEcimitqJzP-dEH9bpURPW5r89bShC5iZ-9szMdlm1I76-d8j9dZQcyiiqmC5sQr0ntL7-La81E6Ynd8ZA3Kl7MI4hlGpkwoXnqHN-sG7tWZjww

# Clear
kubectl delete -n kube-system ingress/dashboard-ingress
kubectl delete -n kube-system secret/dashboard-ingress-secret

参考：
<https://github.com/kubernetes/dashboard>
[Accessing-Dashboard](https://github.com/kubernetes/dashboard/wiki/Accessing-Dashboard---1.7.X-and-above)
[Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
[ingress-nginx](https://kubernetes.github.io/ingress-nginx/)
[istio SDS](https://istio.io/zh/docs/tasks/traffic-management/secure-ingress/sds/)
[cert-manager quick-start](https://github.com/jetstack/cert-manager/blob/master/docs/tutorials/acme/quick-start/index.rst)
[kubectl proxy](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#proxy/)