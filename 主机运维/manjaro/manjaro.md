## 介绍

 **为了解决ArchLinux的“大bug”，Manjaro应运而生！**

Manjaro基于Arch Linux，继承了Arch Linux滚动更新的特点，可以直接使用AUR上最齐全的软件。

针对ArchLinux的“硬伤”、对新手不友好的“弱点”，Manjaro采用了图形化安装程序，使安装过程非常轻松、人性化，同时也把安装ArchLinux后的大量繁琐工作——安装配置显卡驱动、AUR、X服务、桌面环境、中文输入法、Flash插件、音频解码器、显示管理器等——全都做到位了，为新手解决了大麻烦，为高级用户节省了大量时间。

具体来说，Manjaro在用户友好性上做了如下改进：

-   简单、用户友好的图形化安装程序
-   自动检测计算机的硬件（例如显卡）
-   为系统自动安装必要的软件（例如图形驱动程序）
-   它自己的专用软件仓库，以确保提供完全测试过的稳定的软件包
-   支持轻松安装和使用多个内核。
-   预安装桌面环境
-   独家开发的pamac软件管理器，轻松安装软件和更新您的系统
-   预安装编解码器播放多媒体文件

## 安装


## 输入法

推荐使用`fcitx5`

```shell
sudo pacman -Syy fcitx5 fcitx5-configtool fcitx5-chinese-addons fcitx5-qt fcitx5-gtk fcitx5-pinyin-zhwiki
```

## 美化

### 主题

```
```

### 字体

安装常用的中文字体：
```shell
sudo pacman -S ttf-roboto noto-fonts ttf-dejavu  

# 思源字体  
sudo pacman -S noto-fonts-cjk adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
```

配置字体映射：
编辑`~/.config/fontconfig/fonts.conf` 或者`/etc/fonts/local.conf`
```shell
<?xml version="1.0"?>  
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">  
  
<fontconfig>  
  
    <its:rules xmlns:its="http://www.w3.org/2005/11/its" version="1.0">  
        <its:translateRule translate="no" selector="/fontconfig/*[not(self::description)]"/>  
    </its:rules>  
  
    <description>Manjaro Font Config</description>  
  
    <!-- Font directory list -->  
    <dir>/usr/share/fonts</dir>  
    <dir>/usr/local/share/fonts</dir>  
    <dir prefix="xdg">fonts</dir>  
    <dir>~/.fonts</dir> <!-- this line will be removed in the future -->  
  
    <!-- 自动微调 微调 抗锯齿 内嵌点阵字体 -->  
    <match target="font">  
        <edit name="autohint"> <bool>false</bool> </edit>  
        <edit name="hinting"> <bool>true</bool> </edit>  
        <edit name="antialias"> <bool>true</bool> </edit>  
        <edit name="embeddedbitmap" mode="assign"> <bool>false</bool> </edit>  
    </match>  
  
    <!-- 英文默认字体使用 Roboto 和 Noto Serif ,终端使用 DejaVu Sans Mono. -->  
    <match>  
        <test qual="any" name="family">  
            <string>serif</string>  
        </test>  
        <edit name="family" mode="prepend" binding="strong">  
            <string>Noto Serif</string>  
        </edit>  
    </match>  
    <match target="pattern">  
        <test qual="any" name="family">  
            <string>sans-serif</string>  
        </test>  
        <edit name="family" mode="prepend" binding="strong">  
            <string>Roboto</string>  
        </edit>  
    </match>  
    <match target="pattern">  
        <test qual="any" name="family">  
            <string>monospace</string>  
        </test>  
        <edit name="family" mode="prepend" binding="strong">  
            <string>DejaVu Sans Mono</string>  
        </edit>  
    </match>  
  
    <!-- 中文默认字体使用思源宋体,不使用 Noto Sans CJK SC 是因为这个字体会在特定情况下显示片假字. -->  
    <match>  
        <test name="lang" compare="contains">  
            <string>zh</string>  
        </test>  
        <test name="family">  
            <string>serif</string>  
        </test>  
        <edit name="family" mode="prepend">  
            <string>Source Han Serif CN</string>  
        </edit>  
    </match>  
    <match>  
        <test name="lang" compare="contains">  
            <string>zh</string>  
        </test>  
        <test name="family">  
            <string>sans-serif</string>  
        </test>  
        <edit name="family" mode="prepend">  
            <string>Source Han Sans CN</string>  
        </edit>  
    </match>  
    <match>  
        <test name="lang" compare="contains">  
            <string>zh</string>  
        </test>  
        <test name="family">  
            <string>monospace</string>  
        </test>  
        <edit name="family" mode="prepend">  
            <string>Noto Sans Mono CJK SC</string>  
        </edit>  
    </match>  
  
    <!-- 把Linux没有的中文字体映射到已有字体，这样当这些字体未安装时会有替代字体 -->  
    <match target="pattern">  
        <test qual="any" name="family">  
            <string>SimHei</string>  
        </test>  
        <edit name="family" mode="assign" binding="same">  
            <string>Source Han Sans CN</string>  
        </edit>  
    </match>  
    <match target="pattern">  
        <test qual="any" name="family">  
            <string>SimSun</string>  
        </test>  
        <edit name="family" mode="assign" binding="same">  
            <string>Source Han Serif CN</string>  
        </edit>  
    </match>  
    <match target="pattern">  
        <test qual="any" name="family">  
            <string>SimSun-18030</string>  
        </test>  
        <edit name="family" mode="assign" binding="same">  
            <string>Source Han Serif CN</string>  
        </edit>  
    </match>  
      
    <!-- Load local system customization file -->  
    <include ignore_missing="yes">conf.d</include>  
    <!-- Font cache directory list -->  
    <cachedir>/var/cache/fontconfig</cachedir>  
    <cachedir prefix="xdg">fontconfig</cachedir>  
    <!-- will be removed in the future -->  
    <cachedir>~/.fontconfig</cachedir>  
  
    <config>  
        <!-- Rescan in every 30s when FcFontSetList is called -->  
        <rescan> <int>30</int> </rescan>  
    </config>  
  
</fontconfig>
```
运行`fc-cache -fv`命令更新字体缓存，使配置生效。

## GPU

### intel 核显

查看核显使用情况：
```shell
sudo pacman -Syy intel-gpu-tools

sudo intel-gpu-top
```

## 系统备份

推荐使用 [Timeshift](https://teejeetech.com/timeshift/ "timeshift")

## 代理

```shell
sudo pacman -Syy clash
```