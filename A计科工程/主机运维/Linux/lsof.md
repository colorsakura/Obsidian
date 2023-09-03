---
date: 2023-09-02 17:08
tags: linux
layout: note
---

# LSOF 简易教程

Lsof(list open files)是一个查看当前系统文件的工具。在 linux 环境下，任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。如传输控制协议 (TCP) 和用户数据报协议 (UDP) 套接字等，系统在后台都为该应用程序分配了一个文件描述符，该文件描述符提供了大量关于这个应用程序本身的信息。

lsof 打开的文件可以是：

- 普通文件
- 目录
- 网络文件系统的文件
- 字符或设备文件
- (函数)共享库
- 管道，命名管道
- 符号链接
- 网络文件（例如：NFS file、网络 socket，unix 域名 socket）
- 还有其它类型的文件

---

## 命令参数

- -a 列出打开文件存在的进程
- -c<进程名> 列出指定进程所打开的文件
- -g 列出 GID 号进程详情
- -d<文件号> 列出占用该文件号的进程
- +d<目录> 列出目录下被打开的文件
- +D<目录> 递归列出目录下被打开的文件
- -n<目录> 列出使用 NFS 的文件
- -i<条件> 列出符合条件的进程。（4、6、协议、:端口、 @ip ）
- -p<进程号> 列出指定进程号所打开的文件
- -u 列出 UID 号进程详情
- -h 显示帮助信息
- -v 显示版本信息

### Help Information

```shell
lsof: illegal option character: -
lsof: -e not followed by a file system path: "lp"
lsof 4.98.0
 latest revision: https://github.com/lsof-org/lsof
 latest FAQ: https://github.com/lsof-org/lsof/blob/master/00FAQ
 latest (non-formatted) man page: https://github.com/lsof-org/lsof/blob/master/Lsof.8
 usage: [-?abhHKlnNoOPRtUvVX] [+|-c c] [+|-d s] [+D D] [+|-E] [+|-e s] [+|-f[gG]]
 [-F [f]] [-g [s]] [-i [i]] [+|-L [l]] [+m [m]] [+|-M] [-o [o]] [-p s]
 [+|-r [t]] [-s [p:s]] [-S [t]] [-T [t]] [-u s] [+|-w] [-x [fl]] [--] [names]
Defaults in parentheses; comma-separated set (s) items; dash-separated ranges.
  -?|-h list help          -a AND selections (OR)     -b avoid kernel blocks
  -c c  cmd c ^c /c/[bix]  +c w  COMMAND width (9)    +d s  dir s files
  -d s  select by FD set   +D D  dir D tree *SLOW?*   +|-e s  exempt s *RISKY*
  -i select IPv[46] files  -K [i] list|(i)gn tasKs    -l list UID numbers
  -n no host names         -N select NFS files        -o list file offset
  -O no overhead *RISKY*   -P no port names           -Q allow failed search
  -R list paRent PID       -s list file size          -t terse listing
  -T disable TCP/TPI info  -U select Unix socket      -v list version info
  -V verbose search        +|-w  Warnings (+)         -X skip TCP&UDP* files
  -H human readable size   -- end option scan
  -E display endpoint info              +E display endpoint info and files
  +f|-f  +filesystem or -file names     +|-f[gG] flaGs
  -F [f] select fields; -F? for help
  +|-L [l] list (+) suppress (-) link counts < l (0 = all; default = 0)
                                        +m [m] use|create mount supplement
  +|-M   portMap registration (-)       -o o   o 0t offset digits (8)
  -p s   exclude(^)|select PIDs         -S [t] t second stat timeout (15)
  -T fqs TCP/TPI Fl,Q,St (s) info
  -g [s] exclude(^)|select and print process group IDs
  -i i   select by IPv[46] address: [46][proto][@host|addr][:svc_list|port_list]
  +|-r [t[m<fmt>]] repeat every t seconds (15);  + until no files, - forever.
       An optional suffix to t is m<fmt>; m must separate t from <fmt> and
      <fmt> is an strftime(3) format for the marker line.
  -s p:s  exclude(^)|select protocol (p = TCP|UDP) states by name(s).
  -u s   exclude(^)|select login|UID set s
  -x [fl] cross over +d|+D File systems or symbolic Links
  names  select named files or files on named file systems
Only root can list all files; /dev warnings disabled; kernel ID check disabled.
```

## 实例

- 查找某个文件的进程
  `lsof [file]`

- 列出某个用户打开的文件信息
  `lsof -u [username]`

- 列出某个进程打开的所有文件信息
  `lsof -c [process]`

- 列出所有网络连接
  `lsof -i`

- 查看某个端口被谁占用
  `lsof -i:[port]`

## 参考文档

[LSOF Source Code](https://github.com/lsof-org/lsof)
[LSOF 教程](https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/lsof.html)
