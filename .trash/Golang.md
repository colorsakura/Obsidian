---
UID: 0babe57e-7aad-11ec-8db3-386893b59cc5
Alias: [golang]
Type: Notes
---

[[Go学习笔记]]

## 概述

-   [Go](https://golang.google.cn/) is an open source programming language supported by Google
-   Easy to learn and get started with
-   Built-in concurrency and a robust standard library
-   Growing ecosystem of partners, communities, and tools

## Hello World

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}
```
程序结构：包名（必须）+ 引用包 + 代码   顺序固定。
`package main` go 每个文件都属于一个包，同一个包共享变量和函数。Go 程序从`main` package 开始执行。这是一个特殊的 package，用于想要执行的程序。其他包简称为pkg(package)。

## 程序结构

### 命名

### 数据结构

#### 数组与切片(Slice)

1. **数组是一个由固定长度的特定类型元素组成的序列，一个数组可以由零个或多个元素组成。**

`[3]int`  `[4]int `是不同的两种数据类型。
数组的每个元素可以通过索引下标来访问，索引下标的范围是从0开始到数组长度减1的位置。内置的len函数将返回数组中元素的个数。

```go
var array [3]int
var array [3]int = 3int{1, 2, 3}
array := [...]int{1, 2, 3}
```

定义了一个含有100个元素的数组r，最后一个元素被初始化为-1，其它元素都是用0初始化。

```go
r := [...]int{99: -1}
```

当调用一个函数的时候，函数的每个调用参数将会被赋值给函数内部的参数变量，所以函数参数变量接收的是一个复制的副本，并不是原始调用的变量。因为函数参数传递的机制导致传递大的数组类型将是低效的，并且对数组参数的任何的修改都是发生在复制的数组上，并不能直接修改调用时原始的数组变量。在这个方面，Go语言对待数组的方式和其它很多编程语言不同，其它编程语言可能会隐式地将数组作为引用或指针对象传入被调用的函数。

当然，我们可以显式地传入一个数组指针，那样的话函数通过指针对数组的任何修改都可以直接反馈到调用者。下面的函数用于给`[32]byte`类型的数组清零：

```go
func zero(ptr *[32]byte) {
    for i := range ptr {
        ptr[i] = 0
    }
}
```

其实数组字面值`[32]byte{}`就可以生成一个32字节的数组。而且每个数组的元素都是零值初始化，也就是0。因此，我们可以将上面的zero函数写的更简洁一点：

```go
func zero(ptr *[32]byte) {
    *ptr = [32]byte{}
}
```

虽然通过指针来传递数组参数是高效的，而且也允许在函数内部修改数组的值，但是数组依然是僵化的类型，因为数组的类型包含了僵化的长度信息。上面的zero函数并不能接收指向`[16]byte`类型数组的指针，而且也没有任何添加或删除数组元素的方法。由于这些原因，除了像SHA256这类需要处理特定大小数组的特例外，数组依然很少用作函数参数；相反，我们一般使用slice来替代数组。

2. Slice（切片）代表变长的序列，序列中每个元素都有相同的类型。一个slice类型一般写作[]T，其中T代表slice中元素的类型；

![[go-slice 20220123112805.png]]