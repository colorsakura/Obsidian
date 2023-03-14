---
Date: 2023-03-09 12:38
Tag: KVM, Linux, TODO, Doc
---

# KVM

KVM(Kernel-based Virtual Machine)，是内建在 Linux Kernel 中的 hypervisor。相对于另一种开源虚拟化 Xen，
KVM 更为简单和易用，并且和原生 QEMU 不同，KVM 通过一个内核模块使用 CPU 硬件扩展(HVM)来运行一个特殊运行模式的 QEMU。

通过 KVM，可以在 Host 物理主机上运行多种无需修改的操作系统(Linux, Windows, 甚至 macOS)，每个额虚拟机都有自己私有的
虚拟硬件：网卡、磁盘、显卡等等。注意，KVM 和 Xen 不同之处是 KVM 是作为 Linux 的一部分，采用了常规的 Linux 调度和内存管理，
所以更为小巧和易于使用。同时 KVM 仅支持 x86 hvm（vt/svm 指令集），不需要修改 Guest 操作系统，所以 KVM 并不支持
paravirtualation for CPU，但是 KVM 为了提高 I/O 性能，支持 paravirtualization for device drivers（驱动半虚拟化）。

## 准备工作
