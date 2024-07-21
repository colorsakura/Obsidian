---
date: 2024-07-14 21:44
tags:
  - TODO
  - esp32
  - clion
  - linux
  - platformio
layout: note
---

# ESP32

---

## PlatformIO

[[Clion]] 安装 [PlatformIO](https://platformio.org/) 插件.

### Linux

archlinux 安装 `platformio-core` 包

权限:
`sudo usermod -aG uucp $USER`

### Windows

需要
- python

python [get-platformio.py](https://raw.githubusercontent.com/platformio/platformio-core-installer/master/get-platformio.py)

> [!note] python 需要系统级的，conda 提供的不行

ESP32 的控制台波特率默认为 115200。

在 [[Clion]] 控制台中可能乱码, 需要在 `platformio.ini` 设置波特率 `monitor_speed = 115200`
![[ESP32-devkit-pinout.png]]

## 参考链接