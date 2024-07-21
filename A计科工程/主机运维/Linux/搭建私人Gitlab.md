---
layout: note
date: 2023-03-02 17:07
tags:
  - TODO
---

试用 `gitlab-ce` 搭建私人git管理服务器。
内存要求>4GB

```
podman run --detach --hostname git.52bilibili.com --publish 8883:443 --publish 8880:80 --publish 8882:22 --name gitlab --restart always --volume /srv/gitlab/config:/etc/gitlab --volume /srv/gitlab/logs:/var/log/gitlab --volume /srv/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:latest
```

![[Pasted image 20220131104511.png]]
