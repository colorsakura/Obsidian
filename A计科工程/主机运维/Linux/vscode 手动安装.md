---
date: 2023-03-14 20:38
tags: TODO, vscode, linux
layout: note
---


vscode 无法登陆， vscode 无法输入中文

## 前景

manjaro 官方源安装的 `code-oss`，点击登陆，显示loading ，无法正常使用。snap 商店安装的无法使用中文。
最好的解决办法就是使用官方提供的压缩包安装。

## 安装步骤

- 1. 去往[下载页面](https://code.visualstudio.com/download#)，下载压缩包。（xxx.tar.gz）
- 2. 解压：`tar -xvzf xxx.tar.gz`
- 3. 复制文件：`mv xxx/* /opt/vscode`
- 4. `ln -s /opt/vscode/bin/code /usr/bin/code`
- 5. 添加桌面图标。sudo vim /usr/share/applications/vscode.desktop
- 6. `xdg-settings set default-url-scheme-handler code-oss code-oss-url-handler.desktop`

[解决](https://github.com/MicrosoftDocs/live-share/issues/3153)


![[Pasted image 20220505195744.png]]
![[Pasted image 20220505195834.png]]
