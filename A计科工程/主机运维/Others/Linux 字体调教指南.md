---
date: 2023-03-14 20:41
tags: TODO
---


## Linux 下的字体可以很漂亮! 当然，这里是 Linux，你需要一点小小的配置。


很长时间以来，Linux上的中文字体呈现一直……不容乐观。但是随着 FreeType2 由于专利过期默认开启了高质量的 LCD 优化，以及一批高质量的开源字体的公布，Linux 上的中文字体渲染已经可以和 macOS 扳扳手腕了。

当然，这里是 Linux，你需要一点小小的配置。

（Windows 的字体渲染从技术上来说是相当先进的，问题是在中文字体的选择上实在是不忍直视……）

（其实 macOS 的糊成一片的渲染方式也有它的问题，但这就不在本文讨论范围之内了。）


## 太长不看版

安装字体： `Noto`, `Noto CJK`, `Noto Emoji` 和 `Sarasa Gothic` 。

```bash
# On Arch Linux
sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji ttf-sarasa-gothic
# Do your research on other Linux distros
```

下载 [fonts.conf](https://github.com/szclsya/dotfiles/blob/master/fontconfig/fonts.conf) 并扔进你的 `~/.config/fontconfig` 里面, 重新登录，结束。

## 选择字体

近几年来出现了一大批自由字体（包括工作量极大的 CJK 字体），使 Linux 下的字体选择一下子丰富了起来。如果愿意花一点时间的话，可以去各个字体的网站上看一看，选自己看得最顺眼的。[Fonts - ArchWiki](https://wiki.archlinux.org/index.php/Fonts#Font_packages) 上涵盖了相当多的自由字体，可以从这里选起。

这是我选择字体：

-   拉丁字体
    
    -   **Noto Sans** - 无衬线
        
    -   **Noto Serif** - 衬线
        
    -   **Sarasa Term SC** - 等宽（也可以直接使用 Iosevka Term 以节省空间，但可能会导致 Emacs 中的渲染 bug）
        
        -   这是一种拉丁文字符严格为半宽的字体，中英文混排时很协调。如果不适应这种较瘦的字体风格，则可以尝试使用 **Adobe Source Pro** ， **Cascadia Code** 或 **Fira Code** 。
            
-   中文字体
    
    -   **Noto Sans CJK SC** （又称 思源黑体）
        
    -   **Noto Serif CJK SC** （又称 思源宋体）
        

## 安装字体

这里同样使用 Arch Linux 举例，其他发行版请搜索相应的软件仓库。

-   `noto-fonts` Noto Sans, Noto Serif
    
-   `noto-fonts-cjk` Noto Sans CJK SC, Noto Serif CJK SC
    
-   `noto-fonts-emoji` Noto Emoji
    
-   `ttf-sarasa-gothic` Sarasa Term SC
    

## 配置

现在，什么也不用做，打开一个中文页面，应该已经不会存在豆腐块了[1](https://szclsya.me/zh-cn/posts/fonts/linux-config-guide/#footnote-1)。

然而……

 ![默认配置下的 Installation Guide](https://szclsya.me/img/fonts/archwiki-wrongfont.jpg)

默认配置下的 Installation Guide

看上去不大对劲，不是么？

 ![破案了](https://szclsya.me/img/fonts/archwiki-fontsused.jpg)

破案了

…怎么默认到日语字形上去了.

这就是为什么我们需要 [Fontconfig](https://www.freedesktop.org/wiki/Software/fontconfig/) 。

### 一些基础

Fontconfig 是一个用来 配置 字体渲染的程序。也就是说，fontconfig 本身没有将字体渲染成位图的能力（真正执行这个工作的是 [FreeType](https://www.freetype.org/)），但是它能向程序提供可用字体列表并指导 FreeType 引擎将字体正确地渲染出来。

默认情况下，Fontconfig 的用户配置文件位于 `~/.config/fontconfig/fonts.conf`. 它使用 XML 格式，一个最简单配置文件长这样:

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>

  <!-- 具体设置在这里 -->

</fontconfig>
```

我们要做的就是往里面填写具体的配置.

### 渲染相关参数

```xml
  <!-- 显示器使用的像素排列方式. 现代显示器基本都是用rgb排列. -->
  <match target="font">
    <edit mode="assign" name="rgba">
      <const>rgb</const>
    </edit>
  </match>

  <!-- 字体微调的程度, 可选为 hintnone, hintslight (默认), hintmedium, hintfull. -->
  <!-- 简单来说，更高的 hinting 等级可以使字体更锐利，但同时也会损失更多的细节. -->
  <!-- 如果你的显示器的 DPI 高得不像话 (>=300), 那么就可以关闭 hinting, 因为字体会自然对齐像素. -->
  <match target="font">
    <edit mode="assign" name="hintstyle">
      <const>hintslight</const>
    </edit>
  </match>

  <!-- 抗锯齿. 除非你的屏幕DPI奇高否则建议开启. -->
  <match target="font">
    <edit mode="assign" name="antialias">
      <bool>true</bool>
    </edit>
  </match>
  j
  <!-- 关于lcdfilter, 因为我们在使用 FreeType2 自带的 Harmony 子像素渲染, 应该是不需要设置的. -->
```

DPI 即为 Dots per inch (每英尺点数), 可简单理解为显示器的像素密度。 由于在较低的像素密度下, 字形无法严格对齐像素格, 我们必须通过 `hinting` 和 `antialias (抗锯齿)` 让字形在较低DPI的屏幕上有较好的观感。

为了得到你的显示器的具体 DPI 数值, 可以使用 [DPI Calculator](https://www.sven.de/dpi/) 。

这只是一小部分，但是对于普通使用而言调整这些已经足够。如果还是不满意，可以继续查看 [ArchWiki](https://wiki.archlinux.org/index.php/Font_configuration) 。

### 设置首选字体

依照自己的选则替换即可。

```xml
  <!-- 全局默认中文字体 -->
  <!-- Default font for the zh_CN locale (no fc-match pattern) -->
  <match>
    <test compare="contains" name="lang">
      <string>zh_CN</string>
    </test>
    <edit mode="prepend" name="family">
      <!-- 更改下一行即可, 以下同理 -->
      <string>Noto Sans CJK SC</string>
    </edit>
  </match>

  <!-- 默认无衬线字体 -->
  <!-- Default sans-serif font -->
  <match target="pattern">
    <test qual="any" name="family">
      <string>sans-serif</string></test>
    <edit name="family" mode="prepend" binding="same">
      <string>Noto Sans</string>
    </edit>
  </match>

  <!-- 默认衬线字体 -->
  <!-- Default serif fonts -->
  <match target="pattern">
    <test qual="any" name="family">
      <string>serif</string>
    </test>
    <edit name="family" mode="prepend" binding="same">
      <string>Noto Serif</string>
    </edit>
  </match>

  <!-- 默认等宽字体 -->
  <!-- Default monospace fonts -->
  <match target="pattern">
    <test qual="any" name="family">
      <string>monospace</string>
    </test>
    <edit name="family" mode="prepend" binding="same">
      <string>Sarasa Term SC</string>
    </edit>
  </match>
```

### 设置备用字体

因为我们也会遇到其他语种的文字，需要告诉 Fontconfig 如果特定字符在默认字库里找不到应该去哪里找备用字形.

至于如何查找字体的具体名字，可以使用 `fc-list | grep $fontName` 查询.

```xml
  <!-- Fallback fonts preference order -->
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>Noto Sans</family>
      <family>Noto Sans CJK SC</family>
      <family>Noto Sans CJK TC</family>
      <family>Noto Sans CJK JP</family>
      <family>Noto Sans CJK KR</family>
      <family>Noto Color Emoji</family>
      <family>Noto Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>serif</family>
    <prefer>
      <family>Noto Serif</family>
      <family>Noto Serif CJK SC</family>
      <family>Noto Serif CJK TC</family>
      <family>Noto Serif CJK JP</family>
      <family>Noto Serif CJK KR</family>
      <family>Noto Color Emoji</family>
      <family>Noto Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>monospace</family>
    <prefer>
      <family>Sarasa Term SC</family>
      <family>Sarasa Term TC</family>
      <family>Sarasa Term J</family>
      <family>Noto Color Emoji</family>
      <family>Noto Emoji</family>
    </prefer>
  </alias>
```

### 设置特定英文网站的字体

至此，所有未制定特定字体的程序和网站都应该按照我们规定的优先级选择字体了。但是有一些 Linux 友好的网站会指名选择一些 Linux 下的字体（如 Google 和 GitHub 会指名使用 `Liberation` 系列字体）。如果你认为这样的行为可以接受，则可跳过这一节。但如果你想让这些网站也使用我们的字体优先级顺序，则需要一些额外配置。

在显示设置之后（antialias 块之后）加入如下配置：

```xml
<!-- 将 Liberation 系列对应到我们定义的优先字体上 -->
<!-- Map fonts that are commonly used by web pages to our preferred fonts -->
<match target="pattern">
  <test qual="any" name="family"><string>Liberation Sans</string></test>
  <edit name="family" mode="assign" binding="same"><string>sans-serif</string></edit>
</match>

<match target="pattern">
  <test qual="any" name="family"><string>Liberation Mono</string></test>
  <edit name="family" mode="assign" binding="same"><string>monospace</string></edit>
</match>
```

由于 GitHub 指名了 Helvetica 而大多数发行版的 Fontconfig 的配置将其替换成了 Nimbus Sans，有时浏览器会显示使用了 sans-serif ，但实际上却使用了 Nimbus Sans。如果你不会在任何程序中使用 Nimbus Sans 这个字体的话，一个简单的解决手段是直接在 Fontconfig 中屏蔽 Nimbus Sans:

```xml
  <selectfont>
    <rejectfont>
      <pattern>
        <patelt name="family" >
          <!-- This font is causing problem with GitHub -->
          <string>Nimbus Sans</string>
        </patelt>
      </pattern>
    </rejectfont>
  </selectfont>
```

## 大功告成!

把配置文件保存，打开一个程序(比如浏览器)，应该就可以看到变化了!

还有几点需要注意:

-   有一些比较老的程序会忽略 Fontconfig 的设置，需要修改 [Xresources](https://wiki.archlinux.org/index.php/Font_configuration#Applications_without_fontconfig_support). ArchWiki 上提供的事例配置应该足够了。
    
-   很多程序有自己的字体配置。一般而言，将无衬线字体设置成 `sans-serif`, 将衬线字体设置成 `serif`, 将等宽字体设置成 `monospace`, 就会遵循 fontconfig 的设置了。
    

[1](https://szclsya.me/zh-cn/posts/fonts/linux-config-guide/#footnote-reference-1)
