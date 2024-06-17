
## Windows 替换系统字体

![[windows fonts replace.webp]]

系统——恢复——高级启动——立即重新启动-> 疑难解答->高级选项->命令提示符

`XCOPY C:\Fonts C:\Windows\Fonts`

> [!warning] 操作前务必备份系统字体

> [!note] 盘符会变化

查看盘符：
`diskpart` -> `list vol` -> `exit`

## 