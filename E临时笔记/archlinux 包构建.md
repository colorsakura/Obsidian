---
layout: note
date: "2023-03-12 15:56"
tags: "Arch, Devtools, PKG, TODO"
---

# Arch 包构建

## 环境配置

为了避免其他软件的依赖影响打包环境, arch 提供了 devtools 来配置干净的
虚拟系统环境.

- 安装 `sudo pacman -Sy devtools`

每次重新准备干净 chroot 的过程特别慢，如何可以提速呢？
可以考虑做一个 btrfs 镜像挂载到 /var/lib/archbuild,
devtools 会自动使用 btrfs 来加速这个过程。参考制作过程：

```shell
sudo truncate -s 100G /var/archbuild.img
sudo mkfs.btrfs /var/archbuild.img
sudo mount -o discard,compress=lzo,autodefrag /var/archbuild.img /var/lib/archbuild
echo "/var/archbuild.img /var/lib/archbuild btrfs defaults,discard,compress=lzo,autodefrag 0 0" | sudo tee /etc/fstab
```
