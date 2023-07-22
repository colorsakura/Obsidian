---
layout: note
---
## 安装

1. Linux: 
```bash
sudo apt install openjdk-17-jdk

sudo update-alternatives --config java

sudo vim /etc/environment   #JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64/"

source /etc/environment
```

## 概念

`JDK`：Java Development Kit
`JRE`：Java Runtime Environment

JRE就是运行Java字节码的虚拟机。但是，如果只有Java源码，要编译成Java字节码，就需要JDK，因为JDK除了包含JRE，还提供了编译器、调试器等开发工具。