---
title: 解决UFW和Docker的问题
published: 2025-08-01
description: ''
image: ''
tags: 
  - UFW
  - Docker
category: ''
draft: false 
lang: ''
---
https://github.com/chaifeng/ufw-docker?tab=readme-ov-file#解决-ufw-和-docker-的问题

### 解决 UFW 和 Docker 的问题

目前新的解决方案只需要修改一个 UFW 配置文件即可，Docker 的所有配置和选项都保持默认。

修改 UFW 的配置文件 `/etc/ufw/after.rules`，在最后添加上如下规则：


`#BEGIN UFW AND DOCKER`
`filter`
`:ufw-user-forward - 0:0`
`:ufw-docker-logging-deny - 0:0`
`:DOCKER-USER - 0:0`
`-A DOCKER-USER -j ufw-user-forward`

`-A DOCKER-USER -m conntrack --ctstate RELATED,ESTABLISHED -j RETURN`
`-A DOCKER-USER -m conntrack --ctstate INVALID -j DROP`
`-A DOCKER-USER -i docker0 -o docker0 -j ACCEPT`

`-A DOCKER-USER -j RETURN -s 10.0.0.0/8`
`-A DOCKER-USER -j RETURN -s 172.16.0.0/12`
`-A DOCKER-USER -j RETURN -s 192.168.0.0/16`

`-A DOCKER-USER -j ufw-docker-logging-deny -m conntrack --ctstate NEW -d 10.0.0.0/8`
`-A DOCKER-USER -j ufw-docker-logging-deny -m conntrack --ctstate NEW -d 172.16.0.0/12`
`-A DOCKER-USER -j ufw-docker-logging-deny -m conntrack --ctstate NEW -d 192.168.0.0/16`

`-A DOCKER-USER -j RETURN`

`-A ufw-docker-logging-deny -m limit --limit 3/min --limit-burst 10 -j LOG --log-prefix "UFW DOCKER BLOCK "`
`-A ufw-docker-logging-deny -j DROP`

`COMMIT`
`#END UFW AND DOCKER`

然后重启 UFW，`sudo systemctl restart ufw`。现在外部就已经无法访问 Docker 发布出来的任何端口了，但是容器内部以及私有网络地址上可以正常互相访问，而且容器也可以正常访问外部的网络。**可能由于某些未知原因，重启 UFW 之后规则也无法生效，请重启服务器。**