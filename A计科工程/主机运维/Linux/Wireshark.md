---
layout: note
date: 2023-03-02 17:06
tags: TODO, Net, Wireshark, DOC
---

# Wireshark 教程

## 安装配置

```shell
# archlinux
sudo pacman -S wireshark-qt
```

将用户添加到 wireshark 用户组，避免使用 root 运行。
```shell
sudo usermod -aG wireshark $USER
```

## References

- <https://wiki.archlinux.org/title/Wireshark>
- <https://wiki.wireshark.org/CaptureSetup/CapturePrivileges#most-unixes>
