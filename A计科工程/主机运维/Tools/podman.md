---
layout: note
---
## 常用命令

- 搜索镜像：`podman search alpine`
- 下载镜像：`podman pull alpine`
- 查看镜像：`podman images`
- 查看已运行的容器：`podman ps`
- 运行停止的容器：`podman start [con_name]`

### 配置镜像源

`vim /etc/containers/register.conf`

配置内容：
```
unqualified-search-registries = ["docker.io"]

[[registry]] 
prefix = "docker.io" 
location = "9gzrq6w1.mirror.aliyuncs.com"
```