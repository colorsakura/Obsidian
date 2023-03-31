---
date: 2023-03-01 15:06
tags: FileSystem, Linux, Doc, TODO
---

# Btrfs

## 分区

## 快照

Btrfs 的快照功能不能取代备份，它相当于 Git 版本控制。同一份文件只会存在一份在硬盘。
因此，需要备份的时候需要保存到其他硬盘。

## 备份

- 简单备份

```shell
btrfs send /root_backup | btrfs receive /backup
```

- <mark style="background: #FFB86CA6;">增量备份</mark>

```shell
btrfs send -p /root_backup /root_backup_new | btrfs receive /backup
```

`root_backup` 快照名，`backup` 存放文件夹，`root_backup_new` 增量快照名

## References

- <https://btrfs.readthedocs.io/en/latest/index.html>
- <https://wiki.archlinux.org/title/Btrfs>
