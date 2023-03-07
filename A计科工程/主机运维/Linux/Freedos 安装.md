---
Date: 2023-03-07 16:36
Tag: Linux, qemu, dos, FreeDOS, Doc, TODO
---

# Freedos

## [[qemu]] 安装

前往官网下载最新的镜像. [Freedos](https://www.freedos.org/download/)

```shell
# create freedos.img
qemu-img create -f raw freedos.img 200M

# setup
qemu-system-i386 -rtc base=localtime -hda freedos.img -cdrom FD12CD.iso -boot d

# boot
qemu-system-i386 -rtc base=localtime -hda freedos.img
```

第一次安装时, 需要先对磁盘分区, 分区完成后, 重新进入安装步骤.

> [!note]
> 镜像的格式不要选择 qcow 等其他格式. 镜像大小为100-500M, 如果要安装全部软件和游戏, 至少要500M以上, 否则会安装失败.

## freedos 安装过程

<https://youtu.be/xXkmOwLPpcg>

## 安装软件

## References

<https://en.wikibooks.org/wiki/QEMU/FreeDOS>
