---
layout: note
tags: "fish, shell, linux, arch"
---
## 


## Fish 作为 login shell

当 fish 作为登陆 shell 时，环境变量无法传到 firefox, 导致 fcitx5 无法使用，需要在 `/etc/environment` 添加相关变量。

> [!note]
> 原因其实就是我的 wayfire 是在登陆 shell 时自动启动的，但是环境相关的变量在启动 wayfire 的后面，导致变量无法传递。
