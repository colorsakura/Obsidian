---
layout: note
date: "2023-03-12 15:56"
tags: "archlinux, devtools"
---

# Arch 包构建

## 创建环境

`makepkg` 也可以打包，但是它使用的是本地的环境，无法验证相关依赖是否正确。
使用`devtools`可以创建一个干净的虚拟环境。

```
# 定义环境变量
CHROOT=$HOME/.chroot
mkdir ~/.chroot

# 创建初始环境
# 在 btrfs 上，会创建一个新子卷，删除它 btrfs subvolume delete $CHROOT/root
# @TODO：这里添加了base包，以确保后续更新环境正常
mkarchroot $CHROOT/root base base-devel

# 更新环境
arch-nspawn $CHROOT/root pacman -Syu
```

## 打包

在包含`PKGBUILD`文件目录下，运行：
```shell
makechrootpkg -c -r $CHROOT
```

```
Usage: makechrootpkg [options] -r <chrootdir> [--] [makepkg args]
...
...
Default makepkg args: --syncdeps --noconfirm --log --holdver --skipinteg

Flags:
-h         This help
-c         Clean the chroot before building
-d <dir>   Bind directory into build chroot as read-write
-D <dir>   Bind directory into build chroot as read-only
-t <dir[:opts]>   Mount a tmpfs at directory
-u         Update the working copy of the chroot before building
           This is useful for rebuilds without dirtying the pristine
           chroot
-r <dir>   The chroot dir to use
-I <pkg>   Install a package into the working copy of the chroot
-l <copy>  The directory to use as the working copy of the chroot
           Useful for maintaining multiple copies
           Default: chauncey
-n         Run namcap on the package
-C         Run checkpkg on the package
-T         Build in a temporary directory
-U         Run makepkg as a specified user
```

> [!note] 
> 这个虚拟的环境会复制本地系统的 pacman hook 文件

## 加速

为了避免其他软件的依赖影响打包环境, arch 提供了 devtools 来配置干净的
虚拟系统环境.

- 安装 `sudo pacman -Sy devtools`

每次重新准备干净 chroot 的过程特别慢，如何可以提速呢？
可以考虑做一个 btrfs 镜像挂载到 `/var/lib/archbuild`，
devtools 会自动使用 btrfs 来加速这个过程。参考制作过程：

```shell
sudo truncate -s 100G /var/archbuild.img
sudo mkfs.btrfs /var/archbuild.img
sudo mount -o discard,compress=lzo,autodefrag /var/archbuild.img /var/lib/archbuild
echo "/var/archbuild.img /var/lib/archbuild btrfs defaults,discard,compress=lzo,autodefrag 0 0" | sudo tee /etc/fstab
```

> [!note] 
> 如果本身就是 btrfs 文件系统，是否就不需要这个步骤了
> 在创建 CHROOT 时，自动创建了一个子卷，应该就是用来加速的

## 参考

1. [Archwiki: Building in a clean chroot](https://wiki.archlinux.org/title/DeveloperWiki:Building_in_a_clean_chroot)
2. [在干净的环境里编译软件包](https://felixc.at/2017/08/introduction-to-arch-linux-devtools-build-packages-from-a-clean-chroot/)