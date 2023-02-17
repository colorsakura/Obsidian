# Ubuntu使用dd命令制作U盘系统启动盘

【dd命令是比较推荐的一种Linux环境中制作U盘启动盘的方式，无需安装额外的工具，基本上所有Linux发行版都集成了这个命令。】

制作方法示例：

1.查看U盘设备号，本例使用了8G的U盘，并且知道计算机安装了两块硬盘，那么U盘设备号就可以根据大小和硬盘数量很容易的分辨出来"/dev/sdc"

```
root :~$ sudo fdisk -l
Disk /dev/sdc: 8011 MB, 8011120640 bytes
```

2.如果U盘被自动挂载，请使用U盘设备号先umount

```
root :~$ sudo umount /dev/sdc*
```

3.准备好一个iso文件，使用dd命令将这个iso写入u盘

```
root :~$ sudo dd if=~/ubuntu-16.04-desktop-amd64.iso of=/dev/sdc
# if=后面跟要刻录到u盘的iso文件路径
# of=后面是u盘设备号（不需要带分区号）
# 写入过程是没有数据显示的，只要输出和输入路径没错，耐心等待即可，根据U盘读写速度以及iso文件大小，一般需要5~10分钟左右
```

因为是使用dd命令直接将将iso文件数据写入U盘，所以U盘不包含一个标准的分区表，从而导致系统无法正常的识别其大小，也无法正常使用，使用它安装系统后，如果不需要U盘安装盘了，可以使用下面的方法来恢复U盘。

恢复U盘示例：

```
#操作前请用sudo fdisk -l  查看U盘分区号，请谨慎操作，不要误写硬盘分区，本例U盘分区号是/dev/sdc

#1.使用dd命令，将0写入U盘的前512字节（代表主引导记录中的引导代码和分区表）

sudo dd count=1 bs=512 if=/dev/zero of=/dev/sdc

# count=1，写入一次
# bs=512，写入的大小512（byte）
# if=/dev/zero，从系统0生成器读取0
# of=/dev/sdc，写入到U盘中

#2.使用fdisk分区（最好先使用p查看一下还有没有分区，如果第一步操作无误，p将查看不到分区，如果还保留着原有分区，则删除现有分区，重新创建分区），例如只重新划分了一个分区sdc1

sudo fdisk /dev/sdc

#查看现有分区，如果第一步成功，下面将看不到分区

命令(输入 m 获取帮助)： p

Disk /dev/sdc: 8019 MB

#创建新分区，一路默认回车

命令(输入 m 获取帮助)： n
#将分区更改成NTFS类型，以便Win系统也能识别

[size=1em]
21
命令(输入 m 获取帮助)： t

[size=1em]
22
Selected partition 1

[size=1em]
23
Hex code (type L to list codes): 7

[size=1em]
24
Changed system type of partition 1 to 7 (HPFS/NTFS/exFAT)

#保存

命令(输入 m 获取帮助)： w
#3.重新将U盘拔出插入，如果分区自动挂载，请先卸载，然后对该分区进行格式化，格式化成fat是为了让Windows也能识别（mkfs格式化ntfs非常慢，如果想要格式化成ntfs的，可以用mkfs.ntfs替换）

root :~$ sudo umount /dev/sdc1

root :~$ sudo mkfs.fat /dev/sdc1
```

Ps：dd命令直接将Linux的iso写入U盘就能开机引导的原因是，这些Linux的iso自带了引导文件。