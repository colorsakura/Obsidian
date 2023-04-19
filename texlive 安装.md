---
date: 2023-03-30 19:53
tags: TODO, Tex, Arch
---

# texlive 安装

---

## Arch 官方源

```shell
# 基本包
sudo pacman -S texlive-core

# 无脑安装
sudo pacman -S texlive-most

# 中文支持
sudo pacman -S texlive-langchinese
```

## 其他

默认 TeX Live 的字体是不自动给 Fontconfig 提供字体的，因此若想让 XeTeX 之类的使用他们，需要参考 Arch Wiki 上的方法作符号链接。这里以中文环境默认的字体 Fandol 为例：

```shell
ln -s /usr/share/texmf-dist/fonts/opentype/public/fandol ~/.fonts/OTF/
fc-cache ~/.fonts
mkfontscale ~/.fonts/OTF
mkfontdir ~/.fonts/OTF
```

这样 `fc-list | grep Fandol` 应该就能找到这个字体了。

但此时去编译中文字体的话，默认的 pdf 查看器却依然无法看到汉字，而 Chrome 却能正常显示中文。最后再安装如下包：

```shell
sudo pacman -S poppler-data
```

汉字就可以正常在 evince 之类的默认 pdf 查看器中正常显示了。
