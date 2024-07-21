---
date: 2024-03-13 23:29
tags:
  - TODO
  - linux
  - kde
layout: note
---

# KDE 6 体验报告

2024 年开春时, archlinux 的 KDE 6 发布, 默认启用了 wayland. 曾经尝试过 KDE, 但是因为其多而杂的设置, 最后弃之而选择了 sway. KDE 6 带来了很多特性和优化, 考虑再三后, 决定再一次尝试 KDE.

---

## 备份

为避免后续 KDE 污染我的目录, 提前利用 [[btrfs]] 的特性备份系统.

## 安装

仅安装 [plasma-meta](https://archlinux.org/packages/extra/any/plasma-meta/) 包, 算是最小化安装了. 既然选择使用 DE, 那就启用 SDDM 吧.

```shell
sudo systemctl enable --now sddm.service
```

## 配置

1. Kde 相关配置. [^1]
2. sddm 启用 wayland.[^2]
3. 关闭 File Search 的索引. (Setting->Search->File Search)
4. 启用 `Virtual Keyboard`, 享受在 electron 应用输入中文的快感. (Setting->Keyboard->Virtual Keyboard)

## 问题

- [ ] 任务栏图标偶尔不显示, 与主题无关.
- [ ] 启动 `Terminal=true` 的应用, 光标会 loading 5s.

## 参考链接

[^1]: https://wiki.archlinux.org/title/KDE
[^2]: https://wiki.archlinux.org/title/SDDM#Running_under_Wayland
