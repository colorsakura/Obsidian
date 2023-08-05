---
layout: note
Date: 2023-03-08 11:32
Tag: qemu, linux, Doc, TODO
---

# QEMU

QEMU 是一款开源免费的物理机模拟器和虚拟器. 更多介绍前往官网了解. <https://www.qemu.org/>

## 安装

```shell

```

## 使用

```shell
qemu-system-x86_64 -enable-kvm -machine q35 \
                   -device intel-iommu -cpu host -smp 6 -m 8G \
                   -drive if=pflash,format=raw,readonly=on,file=/usr/share/edk2-ovmf/x64/OVMF_CODE.fd \
                   -drive file=$HOME/KVM/ArchLinux.img,index=0,media=disk,if=virtio \
                   -vga none -device qxl-vga,vgamem_mb=256 -display egl-headless,gl=on \
                   -spice unix=on,addr=/run/user/1000/archlinux.spice.sock,disable-ticketing=on \
                   -device virtio-serial-pci \
                   -device virtserialport,chardev=spicechannel0,name=com.redhat.spice.0 \
                   -chardev spicevmc,id=spicechannel0,name=vdagent

```

```shell
qemu-system-x86_64 -enable-kvm -machine q35 \
                         -device intel-iommu -cpu host -smp 6 -m 8G \
                         -drive if=pflash,format=raw,readonly=on,file=/usr/share/edk2-ovmf/x64/OVMF_CODE.fd \
                         -drive file=$HOME/KVM/ArchLinux.img \
                         -cdrom $HOME/Downloads/archlinux-2023.06.01-x86_64.iso -boot order=d \
                         -display egl-headless,gl=on,rendernode=/dev/dri/renderD128 \
                         -spice unix=on,addr=/run/user/1000/archlinux.spice.sock,disable-ticketing=on \
                         -device virtio-serial-pci \
                         -device virtserialport,chardev=spicechannel0,name=com.redhat.spice.0 \
                         -chardev spicevmc,id=spicechannel0,name=vdagent
```

```shell
qemu-system-x86_64 -enable-kvm -machine q35 \
                             -device intel-iommu -cpu host -smp 6 -m 8G \
                             -drive if=pflash,format=raw,readonly=on,file=/usr/share/edk2-ovmf/x64/OVMF_CODE.fd \
                             -drive file=$HOME/KVM/ArchLinux.img,index=0,media=disk,if=virtio \
                             -cdrom $HOME/Downloads/archlinux-2023.06.01-x86_64.iso -boot order=d \
                             -device virtio-vga-gl -display egl-headless,gl=on,rendernode=/dev/dri/renderD128 \
                             -spice unix=on,addr=/run/user/1000/archlinux.spice.sock,disable-ticketing=on \
                             -device virtio-serial-pci \
                             -device virtserialport,chardev=spicechannel0,name=com.redhat.spice.0 \
                             -chardev spicevmc,id=spicechannel0,name=vdagent
```

virt-viewer 连接 `spice+unix:///run/user/1000/archlinux.spice.sock`

## References

<https://wiki.qemu.org/Main_Page>
<https://wiki.archlinux.org/title/QEMU>
