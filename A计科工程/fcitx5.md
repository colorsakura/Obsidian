---
layout: note
date: 2023-03-16 01:23
tags: fcitx5, rime
---

## Fcitx5 Chinese Add On

### 自定义词库

Fcitx5 拼音输入法的词库是由 `libime` 这个库提供的. 并且拼音和双拼使用同样的词库.
Arch 系统上, 词库目录在 `/usr/share/libime`. 内容如下图.

![[libime词库文件.png]]

- `sc.dict` : 通用拼音词库
- `extb.dict ` : CJK 扩展词库
- `zrm.main.dict` : 自然码码表词库 (非拼音词库)

工具: `libime_pinyindict` txt 和 dict 互相转换工具
格式: 词 拼音 词频

```txt
我 wo 0
我们 wo'meng 0
```

> [!note]
> 词频建议全部为 0, 不然无法转换, 原因暂时不明。
> 拼音不支持不正确的拼音格式。

### 中英混合输入

经过测试, 这个功能是由 `spell` 这个选项提供的.

### 用户输入历史

位置: `~/.local/share/fcitx5/pinyin`

## Fcitx5 Rime

Rime 输入法可自定义程度较高，同时学习成本也高。
目前使用其他大佬维护的方案。

### 自定义词库

推荐使用[雾凇拼音](https://github.com/iDvel/rime-ice)

```shell
pkill fcitx5
# 删除默认设置
rm ~/.local/share/fcitx5/rime
# 克隆远程仓库
git clone htts://github.com/iDvel/rime-ice.git ~/.local/share/fcitx5/rime
```

不推荐去修改定制配置，雾凇拼音已经做的很好了，没有必要去浪费时间去折腾一个输入法。

```shell
cd ~/.local/share/fcitx5/rime
git checkout -b zrm
# 根据配置文件做简单修改
git commit -a
git format-patch commit_id -1
# 保存 patch 文件即可
git checkout main
git apply xxx.patch
```

## 总结

目前这两款输入法体验上没有较大的差距，Rime 支持中英混合输入。
