---
layout: pagelt
title: linux install docker-ce
tags: [experience]
---

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
Environment="HTTP_PROXY=http://127.0.0.1:8100"
# 配置docker镜像仓库
vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
sudo systemctl daemon-reload
systemctl restart docker
# 如果代理配置成功，此命令查看环境配置
systemctl show --property=Environment docker
# 查看mirrors
docker info
# 验证proxy和registry
docker search alpine
```