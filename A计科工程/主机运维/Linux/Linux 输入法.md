---
layout: note
date: 2023-03-02 17:08
tags: TODO, Fcitx5, ibus,
---

> [!warning] 只推荐常用的输入法，并且开发活跃的。目前输入法在 Linux 下属于薄弱项，兼容较差，Jetbrains 系无法正常使用。

## ibus[^1]

ibus 是 Gnome 桌面环境下默认的输入法框架，

### ibus-libpinyin

ibus 下使用较多的中文输入法，颜值和词库都非常出色，支持云词库。

配置环境变量：

`vim ~/.profile`

```shell
export GTK_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
export QT_IM_MODULE=ibus
# 解决 Jetbrains 等中文输入问题
export LC_CTYPE=zh_CN.UTF-8
ibus-daemon -d -x
```

```shell
# 不确定是否需要，但确实是安装后，重启ibus，Jetbrains 可用
sudo dnf install ibus-qt-devel ibus-qt
```

> [!note] Jetbrains 使用官方下载二进制压缩包安装。

https://github.com/libpinyin/ibus-libpinyin/issues/289

> [!note] 当前正在使用的输入法，也是 Gnome 默认的中文输入法。

## fcitx5[^2]

### fcitx5-chinese-addons

https://github.com/RikudouPatrickstar/JetBrainsRuntime-for-Linux-x64

使用较多的中文输入法，颜值和词库都非常出色。

**安装：**

```shell
# Fedora
sudo dnf install fcitx5 fcitx5-chinese-addons fcitx5-configtool fcitx5-autostart
```

### fcitx5-rime

目前输入法已经转移到 fcitx5-rime, 主要原因是备份用户词库简单. 随着使用时间的推移, 词库会越来越适合自己.虽然配置文件比较复杂, 但是网上有比较完善的成品方案, 后续可以慢慢地修改, 使其达到完美.

[^1]: [ibus 详细介绍-archwiki](https://wiki.archlinuxcn.org/wiki/IBus)
[^2]: [fcitx5 详细介绍-archwiki](https://wiki.archlinuxcn.org/wiki/Fcitx5)
