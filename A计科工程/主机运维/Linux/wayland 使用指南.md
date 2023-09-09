---
layout: note
date: 2023-02-08 17:46
tags: TODO, wayland, wayfire, x11, electron
---

虽然 wayland 发展迅速，替换 x11 已是板上钉钉，但是目前还是存在很多问题，而且离不开 wayland。

在尝试一段时间的 pure wayland 后，我当前遇到的问题有两个：
- electron 应用无法使用输入法（应用相当多，无法舍弃）
- jetbrains 系软件无法使用（牺牲体验，可替换）

## xwayland

### xwayland 缩放

- 使用**依云**打包的xwayland（xorg-xwayland-lily-git/xorg-xwayland-hidpi-git）
- electron 应用使用 `--force-device-scale-factor=2` 强制设置缩放