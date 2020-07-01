---
layout: pagelt
title: Pod测试DNS和设置http代理访问
tags: [experience]
---

Pod测试DNS和设置http代理访问

(test on 246)

docker 测试
```bash
docker run -it radial/busyboxplus:curl /bin/sh
curl 10.6.78.228
curl www.baidu.com
docker run -it -e http_proxy=10.9.26.13:8080 radial/busyboxplus:curl /bin/sh
export no_proxy=10.*
curl www.baidu.com
```

```bash
kubectl run curl --image=radial/busyboxplus:curl -it
nslookup kubernetes.default
# 再次进入podname
kubectl exec -it curl-785c77797-xq7cz -- /bin/sh
# 删除deployment curl
kubectl delete deployment curl
```

# 可选： 通过configMap给Pod设置环境变量http_proxy访问外网
cat > cm_proxy.yaml <<EOF
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-proxy
data:
  proxy: http://wang.weili:wwl%403345@10.9.26.13:8080
  noproxy: 127.0.0.1,localhost,10.0.0.0/24
EOF
kubectl create -f cm_proxy.yaml
kubectl get cm cm-proxy -oyaml
# 给deployment添加env，代理环境变量, PS：cm-proxy修改后pod env未自动生效
kubectl edit deployment curl
##
spec:
      containers:
      - image: radial/busyboxplus:curl
        imagePullPolicy: IfNotPresent
        name: curl
        resources: {}
        stdin: true
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        tty: true
        env:
        - name: http_proxy
          valueFrom:
            configMapKeyRef:
              key: proxy
              name: cm-proxy
        - name: https_proxy
          valueFrom:
            configMapKeyRef:
              key: proxy
              name: cm-proxy
        - name: no_proxy
          value: "127.0.0.1,localhost,10.0.0.0/24"
# 查看部署更新
kubectl rollout status deployment curl
# 查看更新历史
kubectl rollout history deployment/curl
# 回滚更新
kubectl rollout undo deployment/curl
```

参考：
- [k8s中hostname, hosts文件, DNS和代理问题, service和pod的访问问题](https://blog.csdn.net/luanpeng825485697/article/details/84108166)