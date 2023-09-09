---
date: 2023-09-02 13:50
tags: boot, linux, systemd-boot
layout: note
---

# Boot a .iso directly using [[systemd-boot]]

---

Pretty self-explanatory.

I'm using systemd-boot, and my esp is /boot.

I want to run the official archlinux.iso directly from my /boot partition.

Steps:

    mount archlinux.iso and copy "vmlinux-linuz" + "initramfs-linux.img" to a newly-created directory at /boot ( eg. /boot/live)

    copy the archlinux.iso to /boot

    create the /boot/loader/entries/live.conf entry with:

```conf
title Archlinux Live

linux /live/vmlinuz-linux

initrd /live/initramfs-linux.img

options img_dev=/dev/nvme0n1p1 img_loop=archlinux.iso copytoram
```

(replace "nvme0n1p1" and "archlinux" above, accordingly)

This way, you can chroot into your system, at any given time, without the need of a bootable usb flash drive.

## Refferences

<https://www.reddit.com/r/archlinux/comments/qy281v/boot_an_archlinux_iso_directly_from_my_boot_using/>
