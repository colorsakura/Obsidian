---
layout: note
---

## 安装输入法

```bash
sudo apt install ibus-rime

sudo apt install librime-data-double-pinyin

```

[配置文件](https://github.com/ssnhd/rime)

## 更换内核

```bash
sudo add-apt-repository ppa:cappelikan/ppa

sudo apt update

sudo apt install mainline
```

## 中文字体显示异常

` sudo vim /etc/fonts/conf.d/64-language-selector-prefer.conf`

该文件配置了 Noto Sans CJK 的优先级。

```
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
        <alias>
                <family>sans-serif</family>
                <prefer>
                        <family>Noto Sans CJK SC</family>
                        <family>Noto Sans CJK JP</family>
                        <family>Noto Sans CJK KR</family>
                        <family>Noto Sans CJK TC</family>
                        <family>Noto Sans CJK HK</family>
                        <family>Lohit Devanagari</family>
                </prefer>
        </alias>
        <alias>
                <family>serif</family>
                <prefer>
                        <family>Noto Serif CJK SC</family>
                        <family>Noto Serif CJK JP</family>
                        <family>Noto Serif CJK KR</family>
                        <family>Noto Serif CJK TC</family>
                        <family>Lohit Devanagari</family>
                </prefer>
        </alias>
        <alias>
                <family>monospace</family>
                <prefer>
                        <family>Noto Sans Mono CJK SC</family>
                        <family>Noto Sans Mono CJK JP</family>
                        <family>Noto Sans Mono CJK KR</family>
                        <family>Noto Sans Mono CJK TC</family>
                        <family>Noto Sans Mono CJK HK</family>
                </prefer>
        </alias>
</fontconfig>
```
