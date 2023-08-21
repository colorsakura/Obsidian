---
layout: note
date: 2023-03-16 01:23
tags: Fcitx5
---

# Fcitx5 自定义词库

## 自定义词库

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

> [!note] 词频建议全部为0, 不然无法转换, 原因暂时不明

## 中文模式下输入英语单词

经过测试, 这个功能是由 `spell` 这个选项提供的.

## 用户输入历史

位置: `.local/share/fcitx5/pinyin`
