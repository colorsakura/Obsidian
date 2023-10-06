---
layout: note
---
dmesg 命令用于显示系统开机信息，可用于诊断系统故障。

内核会将系统开机信息存储在ring buffer中，可以使用dmesg命令来查看，开机信息保存在`/var/log/dmesg`文件中。

## 命令语法

```text
dmesg [options]
```

## 选项说明

```text
-c  #显示信息后，清除ring buffer中的内容
-s<缓冲区大小>  #默认值为8196，刚好等于ring buffer的大小
-n  #设置记录信息的层级
-D  #禁用打印消息到控制台
-E  #启用打印消息到控制台
-h  #打印帮助文本并退出
-k  #打印内核消息
-n  #设置将消息记录到控制台的级别
-r  #打印原始消息缓冲区
-s  #使用多少大小的缓冲区来查询内核环缓冲区。 默认情况下为16392
-T  #打印人类可读时间戳
-t  #不打印内核的时间戳
-u  #打印用户空间消息
-V  #输出版本信息并退出
-x  #将设施和级别（优先级）编号解码为可读的前缀
```

## 应用举例

查看前20行开机信息
`dmesg | head -n 20`

```text
[root@centos7 ~]# dmesg | head -n 20
[    0.000000] Initializing cgroup subsys cpuset
[    0.000000] Initializing cgroup subsys cpu
[    0.000000] Initializing cgroup subsys cpuacct
[    0.000000] Linux version 3.10.0-1127.18.2.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC) ) #1 SMP Sun Jul 26 15:27:06 UTC 2020
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1127.18.2.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto spectre_v2=retpoline rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet LANG=en_US.UTF-8
[    0.000000] Disabled fast string operations
[    0.000000] e820: BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009ebff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009ec00-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000dc000-0x00000000000fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x000000003fedffff] usable
[    0.000000] BIOS-e820: [mem 0x000000003fee0000-0x000000003fefefff] ACPI data
[    0.000000] BIOS-e820: [mem 0x000000003feff000-0x000000003fefffff] ACPI NVS
[    0.000000] BIOS-e820: [mem 0x000000003ff00000-0x000000003fffffff] usable
[    0.000000] BIOS-e820: [mem 0x00000000f0000000-0x00000000f7ffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fec00000-0x00000000fec0ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fee00000-0x00000000fee00fff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fffe0000-0x00000000ffffffff] reserved
[    0.000000] NX (Execute Disable) protection: active
[    0.000000] SMBIOS 2.4 present.
```

查看与内存相关的开机信息
`dmesg | grep -i memory`

```text
[root@centos7 ~]# dmesg | grep -i memory
[    0.000000] Base memory trampoline at [ffff9102c0098000] 98000 size 24576
[    0.000000] crashkernel=auto resulted in zero bytes of reserved memory.
[    0.000000] Early memory node ranges
[    0.000000] PM: Registered nosave memory: [mem 0x0009e000-0x0009efff]
[    0.000000] PM: Registered nosave memory: [mem 0x0009f000-0x0009ffff]
[    0.000000] PM: Registered nosave memory: [mem 0x000a0000-0x000dbfff]
[    0.000000] PM: Registered nosave memory: [mem 0x000dc000-0x000fffff]
[    0.000000] PM: Registered nosave memory: [mem 0x3fee0000-0x3fefefff]
[    0.000000] PM: Registered nosave memory: [mem 0x3feff000-0x3fefffff]
[    0.000000] Memory: 972140k/1048576k available (7784k kernel code, 524k absent, 75912k reserved, 5958k data, 1980k init)
[    0.000000] please try 'cgroup_disable=memory' option if you don't want memory cgroups
[    0.755288] Initializing cgroup subsys memory
[    1.619645] x86/mm: Memory block size: 128MB
[    3.669071] Freeing initrd memory: 20628k freed
[    3.933907] Non-volatile memory driver v1.3
[    3.935079] crash memory driver: version 1.1
[    4.000261] Freeing unused kernel memory: 1980k freed
[    4.001692] Freeing unused kernel memory: 396k freed
[    4.003171] Freeing unused kernel memory: 540k freed
[    5.956205] [drm] Max dedicated hypervisor surface memory is 0 kiB
[    5.956206] [drm] Maximum display memory size is 32768 kiB
[    5.968934] [TTM] Zone  kernel: Available graphics memory: 497842 kiB
```

查看与磁盘相关的开机信息
`dmesg | grep -i disk`

```text
[root@centos7 ~]# dmesg | grep -i disk
[    0.000000] RAMDISK: [mem 0x357a7000-0x36bcbfff]
[    3.738914] VFS: Disk quotas dquot_6.5.2
[    4.013333] systemd[1]: Running in initial RAM disk.
[    6.295432] sd 0:0:0:0: [sda] Attached SCSI disk
[root@centos7 ~]# dmesg | grep -i sda
[    6.291698] sd 0:0:0:0: [sda] 41943040 512-byte logical blocks: (21.4 GB/20.0 GiB)
[    6.291830] sd 0:0:0:0: [sda] Write Protect is off
[    6.291831] sd 0:0:0:0: [sda] Mode Sense: 61 00 00 00
[    6.292032] sd 0:0:0:0: [sda] Cache data unavailable
[    6.292033] sd 0:0:0:0: [sda] Assuming drive cache: write through
[    6.294046]  sda: sda1 sda2
[    6.295432] sd 0:0:0:0: [sda] Attached SCSI disk
[   15.843965] XFS (sda1): Mounting V5 Filesystem
[   16.778908] XFS (sda1): Starting recovery (logdev: internal)
[   16.801659] XFS (sda1): Ending recovery (logdev: internal)
```