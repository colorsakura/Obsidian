---
Date: 2023-03-07 16:22
Tag: Go, Doc
---

Go 是一门开源的、表现力强的、简洁的、静态编译型语言。它在语言**级别支持协程**（Coroutine），让你轻松的编写并发性代码。Go 新颖的类型系统便于构建灵活的、**模块化**的应用程序。Go 能够快速的**编译为机器码**，同时支持**垃圾回收（并发标记清除）**、**运行时反射**等现代语言特性。

多年来系统级编程语言没有出现新成员，然而计算领域发生重大的变化：

1. 计算机的速度极大的增快了，而软件开发速度的变化不大

2. 当今的软件开发中，依赖管理是一项重要的工作。C 风格语言的基于头文件的依赖难以进行清晰的依赖分析

3. 传统静态类型语言，例如 Java、C++笨重的类型系统让人反感，很多团队转而使用动态类型语言，例如 Python、JavaScript

4. 流行的系统级语言不支持垃圾回收、并行计算等基础概念

Go 致力于解决解决上面几点中提及的问题，它能保证大型程序的快速编译、简化依赖管理、完全支持垃圾回收和并行计算。

Go 提供了一个运行时，此运行时作为每个 Go 应用程序的一部分，提供语言的基础服务，例如垃圾回收、并发、栈管理。这个运行时更像是 libc 而非 JVM，Go 运行时不提供虚拟机。

# 编程元素

## 变量

使用关键字 var 可以定义一个变量，或者变量的列表。**变量可以定义在包级别、函数级别**：

```go
// 包级别的变量列表声明
var c, python, java bool
 
// 声明并赋值
var gender bool = false;
 
func main() {
    // 函数内的单个变量声明
    var i int
    fmt.Println(i, c, python, java)
}
```

### 变量的初始化

你可以在变量列表声明之后紧跟着初始化列表：

```go
// 数量必须一致
var i, j int = 1, 2
// 变量类型可以省略，从初始化表达式中推导
var c, python, java = true, false, "no!"
```

### 短声明变量

使用 `:=` 操作符，可以**在函数体内部声明**变量并初始化，而不需要 var 关键字和变量类型说明（自动推导）：

```go
c, python, java := true, false, "no!"
```

这种语法**不能用在函数体外**，函数外的**每个语句都必须以关键字开头**（例如 var、func）。

### 块级作用域

Go 支持块级作用域，可以覆盖父块的变量声明：

```go
x := 1
fmt.Println(x)     // 打印1
{
    fmt.Println(x) // 使用父作用域的变量，打印1
    x := 2         // 在子作用域重新声明变量
    fmt.Println(x) // 打印2
}
fmt.Println(x)     // 打印1，看不到子作用域的变量
```

### 变量的重复声明

单个变量不能重复声明：

```go
i := 0
i := 1 // 错误
```

但是多变量声明时，**只要其中一个是新变量，就可以重复**声明：

```go
// 重复声明i
i, j := 1, 0  
```

### 零值

如果变量在定义时没有明确的初始化，则**自动初始化为零值**：数值类型初始化为 0；字符串类型初始化为""；布尔型初始化为 false；**interface/map/func/slice/chan 初始化为 nil**。

字符串不能赋值为 nil，它的零值只能是空串：

```go
var x string = nil // 出错
```

你**可以向零值的 slice 中添加元素**：

```go
var s []int
s = append(s,1)
```

但是**不能向零值的 map 添加键值对**。

### 类型转换

使用语法 `T(v)` 可以将 v 转换为 T 类型：

```go
i := 42
f := float64(i)
u := uint(f)
```

需要注意的是，Go 语言在**不同的类型之间进行赋值时，需要显式的类型转换**。

### 类型推导

当定义变量而不显式指定其类型时，变量的类型由赋值符号右侧的子表达式推导：

```go
var i = 0          // 推导为int
j := i             // 推导为int
f := 3.142         // float64
g := 0.867 + 0.5i  // complex128
```

## 基本类型

| 类型 |说明 |
| ----------- |----------- |
| bool |布尔型，取值 true / false |
| string |字符串，双引号包围<br>多行字符串使用反引号包围<br> |
| 定长整数 |对应不同的位数（1、2、4、8 字节）：<br>有符号类型：int8 int16 int32 int64<br>无负号类型：uint8 uint16 uint32 uint64<br> |
| rune |字面意思是“符文”，实际上是 int32 的别名，代表一个 Unicode 代码点（Code Point）<br>代码点和字符唯一性的对应，例如 U+2318（十六进制 2318）对应字符⌘<br>rune 的直接量语法和 Java 中的 char 类型一致：<br>var r rune = '⌘'<br>fmt.Printf ("%v %c %x", r, r, r)&nbsp;&nbsp;// 8984 ⌘ 2318&nbsp;<br> |
| 不定长整数 |int、uint、uintptr 在 32bit 的系统上通常为 32 位；在 64bit 的系统上通常为 64位 |
| 浮点数 |float32、float64 |
| 复数 |complex64、complex128<br>示例：<br>var z complex128 = cmplx.Sqrt (-5 + 12i)<br>const f = "%T (%v)\n"<br>fmt.Printf (f, z, z)&nbsp;&nbsp;// complex128 ((2+3i))<br> |

## 常量

常量类似于变量，但是使用 `const` 关键字声明，常量不支持 `:=` 语法：

```go
const Pi = 3.14
const World = "Hello"
const Truth = true
```

## 字符串

Go 字符串的本质是一个 byte 切片（以及一些额外的属性）。字符串是不可变的。如果需要修改字符串的某个字符，可以使用 byte 切片：

```go
x := "text"
xbytes := []byte(x)
xbytes[0] = 'T'
string(xbytes)
```

但是需要注意，**单个字符可能存放在多个 byte**中。因此更新一段字符串，使用 rune 的 slice 可能更好，尽管单个字符也可能占用多个 rune（例如包含重音符号的字符）。

### 和 byte 切片的转换

当字符串转换为 byte 切片，或者 byte 切片转换为字符串时，你**都会得到原始数据的拷贝**。这和通常的 Cast 语义不同。

### 获取长度

len ()调用获取的是字符串包含的 byte 数量。要获取字符数量，可以：

```go
import "unicode/utf8"
utf8.RuneCountInString(data)
```

实际上 RuneCountInString 获取的是 rune 而非 char 数量，包含重音符号的字符，可能占据两个 rune。

### 获取字符

对字符串进行[]操作，得到的是 byte 而非 rune。要获得 rune，可以使用 for range 操作，或者利用 unicode/utf8、golang. org/x/exp/utf8string 等包：

```go
import "golang.org/x/exp/utf8string"
str := utf8string.NewString("你好")
str.At(0)
```

### 不一定是 UTF8

```go
// 可以使用转义序列引入任意数据
data := "A\xfeC"
utf8.ValidString(data) // false
```

## 函数

Go 的函数可以接收 0-N 个参数：

```go
func add(x int, y int) int {
    return x + y
}
```

可以注意到，变量的类型、函数的返回值类型，都是**后置声明**的。这种与众不同的语法风格，在进行复杂声明时能够保持可读性。

如果连续多个形参的类型相同，则可以**仅仅为最后一个声明类型**：`func add(x, y int) int {}`

### 多值返回

Go 函数可以**返回多个值**：

```go
package main
 
import "fmt"
// 声明多个返回值时，需要用括号包围
func swap(x, y string) (string, string) {
    return y, x
}
 
func main() {
    // 短声明，只能在函数体内使用
    a, b := swap("Wong", "Alex")
    fmt.Println(a, b)  // Alex Wong
}
```

### 返回值命名

一般语言的返回值只能声明类型，Go 的返回值还可以具有名称：

```go
package main
 
import (
    "fmt"
    "strings"
)
                        // 返回值具有名称，相当于定义了局部变量
func split(name string) (first, last string) {
    na := strings.Split(name," ")
    // 直接为返回值赋值
    first = na[0]
    last = na[1]
    // 空白的return语句意味着依次返回，相当于 return first, last
    return
}
 
func main() {
    fmt.Println(split("Alex Wong"))
}
```

### 作为值使用

函数可以像任何值一样，被传递来传递去：

```go
// 函数作为形参，不需要声明其参数、返回值的名字
func add(x, y int, adder func(int, int) int) int {
    return adder(x, y)
}
func main() {
    // 函数作为变量
    adder := func(x, y int) int {
        return x + y
    }
    // 函数作为实参
    fmt.Println(add(1, 2, adder))
}
```

### 闭包

所谓闭包，是指一个函数值（作为值的函数，Function Value）引用其外部作用域的变量。这导致即使外部作用域生命周期结束，被引用的变量仍然存活。

```go
func genCounter() func() int {
    count := 0
    return func() int {
        count++ // 每次调用导致被封闭的变量值增加
        return count
    }
}
func main() {
    counter := genCounter();
    for i := 0; i < 10; i += 1 {
        fmt.Println(counter())
    }
}
```

### 可变参数

Go 函数支持不定数量的参数，例如：

```go
import "fmt"
 
func print(args ...interface{}) {
    for index, value := range args {
        fmt.Printf("%v = %T %v\n", index, value, value)
    }
    // 0 = int 1
    // 1 = int 2
    // 2 = string 一
}
func main() {
    print(1, 2, "一")
}
```

切片可以展开为可变参数列表：

```go
func printAll(strs ...string) {
    for _, str := range strs {
        fmt.Printf("%s ", str)
    }
}
func main() {
    printAll([]string{"A", "B", "C"}...) // 展开
}
```

## 指针

Go 语言支持指针，指针保存变量的地址。取地址、解引用的操作符也被支持：

```go
var i int = 10
// 取地址，获得变量的指针
var ip *int = &i
// 解引用
// 通过指针来写变量
*ip = 5
// 通过指针来读变量
fmt.Print(*ip) // 5
```

但是，Go 语言不支持指针的运算。

注意：**返回局部变量的指针是安全的，函数返回后此变量仍然存活**，这和 C 语言不同。

### 传值和传引用

如果函数的入参是**结构**，而非结构的指针，则**实际传递的是结构的副本，所有字段均被浅拷贝**。

通过**make、new、&**获得的对象，你不需要担心拷贝副本的开销或修改无效。也就是说，**传递“引用”的类型包括切片、map、通道、指针、函数**。**string 类型是不可变的，也按引用传递**。**数字、bool、结构等都是按值**传递。

**数组本身是传值**的，作为**函数参数时，你通常使用切片而非数组**。

## 结构

Go 语言支持结构体，也就是字段的集合：

```go
type Vector3 struct {
    X int
    Y int
    Z int
}
 
func main() {
    fmt.Print(Vector3{1, 2, 3}) // {1 2 3}
}
```

要访问结构中的字段，使用点号：

```go
v := Vector3{1, 2, 3}
fmt.Print(v.X)
```

可以通过结构的指针来访问字段，语法和上面一样：

```go
v := Vector3{1, 2, 3}
pv := &v
fmt.Print(pv.X)
```

声明结构的不同方式：

```go
// 列出所有字段
v1 := Vector3{1, 2, 3}
// 不列出任何字段
v2 := Vector3{}
// 仅仅列出部分字段
v3 := Vector3{Z: 3}
// 对直接量取地址
pv4 := &Vector3{Z: 3}
fmt.Printf("%v %v %v %v", v1, v2, v3, *pv4)
// {1 2 3} {0 0 0} {0 0 3} {0 0 3}
```

### 字段标签

使用标签（Tag）你可以为结构的字段添加元数据。这些元数据可以通过反射调用获得。在决定如何存储结构到数据库，或者串行化为 JSON/XML 等格式时，常常利用字段标签。

字段标签通常是 `key1:"value1" key2:"value2"` 这种形式的键值对，例如：

```go
type User struct {
    Name string `json:"userName" xml:"user-name"`
}
```

如果值部分包含更多信息，可以使用逗号分隔：

```go
Name string `json:"name,omitempty"`
```

经常使用的标签键包括：

| 标签键 |说明 |
| ----------- |----------- |
| json |包 encoding/json 使用此键，具体查看 json.Marshal () |
| xml |包 encoding/xml 使用此键，具体查看 xml.Marshal () |
| bson |包 gobson 使用此键，具体查看 bson.Marshal () |
| protobuf |包 github. com/golang/protobuf/proto 使用此键 |
| yaml |包 gopkg. in/yaml. v2 使用此键，具体查看 yaml.Marshal () |
| db |包 github. com/jmoiron/sqlx 使用此键 |
| orm |包 github. com/astaxie/beego/orm 使用此键 |
| valid |包 github. com/asaskevich/govalidator 使用此键 |
| schema |包 github. com/gorilla/schema 使用此键 |
| csv |包 github. com/gocarina/gocsv 使用此键 |

### 空结构

`struct{}` 的特点是**占用内存空间为零**：

```go
var s struct{}
fmt.Println(unsafe.Sizeof(s)) // 0
```

因此可以用它作为占位符：

```go
chan struct{}        // 仅仅用来传递信号，而非读写数据
map[string]struct{}  // 实现Set结构 
```

## 数组

类型 `[n]T` 表示具有 n 个元素的 T 类型的数组。数组的长度是其类型的组成部分，这和 C 语言类似：

```go
var a [2]string
fmt.Println(a[0], a[1]) //  空白
a[0] = "Hello"
a[1] = "World"
fmt.Println(a[0], a[1]) //  Hello World
fmt.Println(a)          //  [Hello World]
 
//  直接量语法
a = [2]string{"Hello","World"}
 
// 只初始化指定的索引
array := [10]int{1:3,3:1}
// 自动推断数组长度
array:=[...]int{1,2,3,4,5}
 
// 遍历数组的两种方式
for i:=0; i<len(array); i++{
    fmt.Println(array[i])
}
for index,value := range array{
    ...
}
 
// 指针的数组
 
var ia [10]*int{9:new(int)}  // 为索引9分配内存
*ia[9] = 10   // 解引用并赋值，已经分配内存的索引才能被赋值
```

关于**数组，需要注意它的长度是一定的**，这和切片不同**。元素类型相同、长度也相同的数组，它们的类型才是一样的**。

你**可以对数组元素进行取地址操作**：

```go
// 避免复制
container := &dep.Spec.Template.Spec.Containers[0]
```

数组在**传参、赋值时是传值**，不过参数通常都会用切片。

### 数组是数值

在 C++中，数组即指针。函数的入参是数组时，函数内外引用的是相同内存区域。

但是在 Go 中，数组是值，向函数传递数组时，函数**得到的是原数组的拷贝**。也就是说你无法修改原始数组。如果需要修改原始数据，则需要传递其指针：

```go
x := [3]int{1,2,3}
 
func(arr *[3]int) {
  // 解引用
  (*arr)[0] = 0
}()
```

或者，你可以使用切片。尽管切片本身是传值的，但是**其底层数组在函数内外共享**：

```go
x := []int{1,2,3}
func(arr []int) {
  arr[0] = 7
}(x)
```

但需注意，**这种方法不能用于增加切片元素，因为其底层数组可能被换掉**。

## 切片

`[]T` 表示类型为 T 的切片。切片类似于数组，实际上它的底层存储就是数组。**切片可以用来实现动态长度的数组**。

切片是一个很简单的数据结构，它包括三个成员：指向底层数组的指针；切片长度；切片容量。

```go
// 直接量语法
s := []int{1, 2, 3, 4, 5}
// 切片具有长度、容量两个属性
// 长度是切片包含元素的数量
fmt.Println(len(s))   // 5
fmt.Println(s[4])     // 5
// 容量则是切片的底层数组的长度
fmt.Printlkn(cap(s))  // 5
 
// 通过make函数创建切片，长度5，底层数组长度（容量）10
s := make([]int, 5, 10)
 
// 切片也支持仅初始化指定索引的值
s := []int{3:4}
```

切片元素可以是任何类型，包括切片：

```go
matrix := [][]int{
    []int{1, 2, 3},
    []int{4, 5, 6}, // 如果花括号写在下一行，这此行尾部需要有逗号
}
for i := 0; i < len(matrix); i++ {
    row := matrix[i]
    for j := 0; j < len(row); j++ {
        fmt.Printf("%v ", row[j])
    }
    fmt.Println()
}
```

Go 支持类似于 Python 的切片操作，`s[lo:hi]` 表示产生从索引 lo（包含）到 hi（不包含）的新切片。需要注意，进行切片操作后，新产生的**切片会共享底层数组**：

```go
s := []int{1, 2, 3, 4, 5}
fmt.Println(s[2:3])  // [3]
// 省略hi则切到尾部
fmt.Println(s[2:]) // [3 4 5]
 
var a [10]int
# 以下表达式等价
a[0:10]
a[:10]
a[0:]
a[:]
```

要构造切片，可以调用 make 函数：

```go
// 构造初始长度为10，容量为100的切片
s := make([]int, 10, 100)
fmt.Print(len(s)) // 10
```

切片的零值是 nil，nil 切片的长度、容量皆为 0。另外空切片的长度容量也都是0：

```go
// nil切片，底层数组的指针为nil
var nilSlice []int
// 空切片，底层数组的指针为一个地址
slice:=[]int{}
```

要向切片的尾部添加元素，可以调用 append 函数。这个函数有可能导致创建新的底层数组。

```go
// 向切片s添加1-N个元素，返回包含s的原元素和所有新元素的切片
// 如果s的底层数组太小，会自动分配一个大的数组，返回的切片会指向这个新数组
func append(s []T, vs ...T) []T
 
s := []int{1}
s1 := append(s, 2, 3, 4)
fmt.Print(s1) // [1 2 3 4]
```

在创建**新切片时，最好让切片的长度和容量一致**，这样 append 操作总会产生新的底层数组，这可以避免因为共享底层数组导致的奇怪问题。

for... range 格式的循环，可以用来迭代切片（以及 map）：

```go
s := []int{1, 2, 3, 4, 5}
//  索引，值
for i, v := range s {
    fmt.Printf("%d=%d ", i, v)
}
// 如果不希望迭代索引或值，可以用 _ 代替之
for i, _ := range s {
    fmt.Printf("%d", i)
}
// 如果不希望迭代值，可以直接省略 _
for v := range s {
    fmt.Printf("%d", v)
}
 
// 注意，值是拷贝出来的，不支持引用，要修改切片元素本身，需要
for i,_ := range s {
    e := &s[i]
    e.field = value
}
```

## 包

任何 Go 应用程序都是由包构成：

1. 程序的执行**入口必须是 main 包，在构建时 go 自动为 main 包创建可执行程序**

2. 程序可以导入一个或者多个包

3. 包的名字通常和导入路径的最后一个目录名一致，也就是 go 源文件中声明的包名和文件所在目录名一样

4. 包可以包含多个源文件

代码示例：

```go
// 声明当前所属包
package main
 
// 导入包，以便使用其中的成员
import (
    "fmt"
    "math/rand"
    "time"
)
// 除了用圆括号一起导入多个包，还可以多次使用import语句分别导入单个包
import "fmt"
import "math/rand"
 
func main() {
    rand.Seed( time.Now().UnixNano())
    fmt.Println("Random number: ", rand.Intn(100))
}
```

### 名称导出

包中的任何名称 —— 定义在全局作用域下的变量或者函数，只要**以大写字母开始，即为导出名称**（Exported names）。

只有导出名称才能够在包外部（import 包的地方）访问。对于**A.B 这样的表达式，只有 A 被导出了 B 才可能被包外部访问**。

没有导出的名称，只能在包内访问，**各源文件都可以自由访问其它源文件中定义的名称**。

### 包导入

要使用一个包，必须使用 import 关键字将其导入。Go 在根据导入路径查找包时，遵循以下优先级：

1. 首先在 `$GOROOT` 下寻找

2. 然后在 `$GOPATH` 下寻找

3. 仍然找不到，尝试进行远程包下载、导入

在导入包的时候，可以赋予其别名，例如：`import util "gmem.cc/util"`。如果想导入包但又不使用其 export 出的成员，可以将其别名为 `_`，这种用法仅仅为了执行包中的 init 函数。

### init 函数

每个源文件都可以定义 init 函数（因此一个包可以有多个 init 函数），进行必要的状态初始化。init 函数的执行时机是：

1. init 所在源文件**所有导入的包被初始化**后

2. 包中声明的**所有变量的初始化式被估算**后

3. 如果目标包中有多个 init 函数，则根据**init 所在源文件的名称的字典序**，依次执行

例如下面这个源文件：

```go
func init() {
    fmt.Println("Initializing...")
}
 
func IsBlank(str string) bool {
    return len(strings.Trim(str, " ")) == 0
}
```

只要其所在的包被使用（如果是 main 包则一定被使用），则 init 函数一定会执行，并且肯定在 IsBlank 被调用之前。

一个较为复杂的 Go 程序中，包初始化顺序如下：

```shell
  go run *.go
# ├── 主包被执行
# ├── 初始化所有导入包
# |  ├── 初始化自己之前，初始化所有导入的包
# |  ├── 然后初始化自己的变量
# |  └── 然后调用自己的init
# └── 主包初始化
#    ├── 初始化全局变量
#    └── 按源文件名称依次执行init函数
```

### 迭代

不管是切片，还是数组、映射，**for range 操作获取的都是元素的拷贝**。要想修改集合元素，需要使用索引：

```go
for i,_ := range data {
    data[i] *= 10
}
```

**如果元素存放的是指针则可以直接解引用并修改**。

## 映射

```go
type LatLng struct {
    Lat, Lng int
}
 
func main() {
    // 声明一个映射：map[KeyType]ValueType
    var locations map[string]LatLng;
 
    // 使用make函数实例化
    locations = make(map[string]LatLng)
 
    // PUT操作
    locations["Alex"] = LatLng{38, 100}
 
    // GET操作
    fmt.Println(locations["Alex"].Lat)
 
    // 测试键是否存在，如果不存在第一个返回值为零值，第二个false
    ll, exist := locations["Alex"]
    fmt.Printf("%v %v", ll, exist) // {38 100} true
 
    // DEL操作
    delete(locations, "Alex")
 
    // 直接量语法
    locations = map[string]LatLng{
        "Alex": LatLng{38, 100},
        "Meng": LatLng{38, 110},
    }
    // for...range循环。注意，遍历的顺序没有任何保证
    for key,value := range locations{
        fmt.Println(key,value)
    }
}
```

### 支持的键类型

布尔值、数字、字符串、指针、通道、接口类型、结构，以及这些类型的数组，都可以作为映射的键。**切片、映射、函数不可以作为映射的键**。

不像 Java，Go 的 map 不支持自定义 equals/hashCode。两个键是否相等，规则如下：

1. 指针：当指针**指向同一变量**时它们相等，nil 指针是相等的

2. 通道：两个通道是由**同一次 make 调用产生**的，则它们相等，nil 通道是相等的

3. 接口：具有**相同的运行时类型，且运行时对象是相等**的，则接口相等

4. 结构：如果**两个结构的，相同名称的非空字段都相等**，则结构相等

5. 数组：如果**每个元素都相等**

### 线程安全

注意，**Go 中 map 的操作不是原子的**，原因是 map 的典型应用场景下不牵涉到跨 Goroutine 的安全访问。

要保证操作的原子性，建议使用 Goroutine+Channel，或者使用 sync 包。

### 不支持对值取地址

注意：**不支持对映射的值进行取地址操作**：

```go
users := map[string]User{
    "Alex": User{"Alex"},
}
alex := &users["Alex"] // 编译错误 
```

### 迭代时删除

注意：在**迭代映射的过程中删除键值是安全的**：

```go
for key := range m {
    if key.expired() {
        delete(m, key)
    }
} 
```

## 方法

尽管没有类（Class）的概念，Go 却支持为类型定义方法。**Go 不支持方法重载**，这意味着两个方法不得具有相同的名字。

方法是一种函数，它具有特殊的接收者（Receiver）参数：

```go
func (receiver Receiver) methodName(args Args) rv ReturnValues{}
 
 
type LatLng struct {
    latitude, longitude float64
}
// 接收者位于func关键字和方法名之间，不和普通的方法参数放在一个列表中
func (ll LatLng) isNorthHemisphere() bool {
    return ll.latitude > 0;
}
func (ll LatLng) isEastHemisphere() bool {
    return ll.longitude > 0;
}
 
func main() {
    jazxPos := LatLng{39.9601241068, 116.4405512810}
    fmt.Println(jazxPos.isEastHemisphere())
}
```

注意：**方法仅仅是具有接收者的函数而已**。

你也可以为其它（非 struct）类型定义方法，但是你**只能为当前包中声明的类型定义方法**：

```go
// 类似于C语言的typedef
type Float float64
 
func (f Float) Abs() Float {
    // 注意下面这种在Float和float64之间转换的语法
    return Float(math.Abs(float64(f)))
}
func main() {
    f := Float(-100)
    fmt.Println(f.Abs())
}
```

### 值作为接收者

这种情况下，你**无法修改源对象，因为传入的是拷贝**。

### 指针作为接收者

可以为指针定义方法，也就是将指针作为方法的接收者。接收者声明为*T 也是为 T 类型定义方法，但是 T 本身不能是指针。

基于**指针接收者定义方法，你就可以修改接收者的状态**，这种行为类似于 C 语言。

```go
// 传值
func (ll LatLng) isEastHemisphere() bool {
    // 此修改对于调用者不可见
    ll.longitude = 0
    return ll.longitude > 0;
}
// 传引用
func (ll *LatLng) isEastHemisphere() bool {
    // 此修改对调用者可见
    ll.longitude = 0
    return ll.longitude > 0;
}
```

由于指针修改接收者状态的能力，第二种形式的方法定义要常用的多，此外第二种形式还**避免了不必要的值拷贝**。一般来说，一个类型上的方法，通常**都使用**指针接收者，或者值接收者，而不会混用。

**调用方法时，你不需要对接收者进行取地址操作**，这和普通函数调用不一样。普通函数的参数如果要求指针，则你必须传入指针。

***T 支持的方法集是：以*T 为接收者的方法集 + 以 T 为接收者的方法集**。因此*T 支持的方法可能比 T 多。

## 接口

**接口是 Go 中进行方法动态绑定（多态）的唯一途径**。针对结构或者其它具体类型的方法调用，其绑定都发生在编译时。

接口这种类型定义一组方法签名：

```go
type GeoOps interface {
    // 接口中的方法不需要 func关键字
    IsNorthHemisphere() bool
    isEastHemisphere() bool
}
```

可以将实现了接口中定义了的方法的类型赋值给接口：

```go
// 以接口类型声明值（Interface Value）
var gops GeoOps
// 是否需要取地址，取决于实现方法的是T还是*T
gops = &jazxPos
fmt.Println(gops.IsNorthHemisphere())
```

### 实现接口

Go 语言不需要显式的 implements 声明，类型只需要实现接口中规定的方法即可（鸭子类型识别）。这种设计让接口和它的实现完全解耦。

需要注意：

1. 实际类型以值接收者实现接口的时候，不管是**实际类型的值，还是实际类型值的指针，都实现了该接口**

2. 实际类型以指针接收者实现接口的时候，**只有指向这个类型的指针才被认为实现了该接口**

### nil 接口值

如果接口值指向 nil 变量，调用方法时，接收者是 nil。在很多语言中，调用 nil 的方法会导致空指针异常，但是在 Go 语言中，你可以优雅的实现如何处理 nil 值的逻辑。

注意 nil 接口值和指向 nil 变量的接口值：

```go
var gops GeoOps;
 
// nil接口值（未赋值给实际变量）导致运行时错误
gops.isEastHemisphere() // panic: runtime error: invalid memory address or nil pointer dereference
 
// 但是让接口值指向nil变量则不会出现上述问题
var ll LatLng
gops = &ll
gops.isEastHemisphere()
```

还有一个陷阱需要注意：

```go
var e *E = nil
var ei error = e
var ni error = nil
println(e == nil)  // true    nil
println(ei == nil) // false   (error, nil)   这三个都是判断变量本身是不是nil，即使将nil变量赋值给指针，指针也不是nil，因为它获得了类型信息
println(ni == nil) // true    nil
```

需要注意：

1. 如果指针实现了接口，则这种指针的零值，可以赋值给接口 ——**这个赋值过程赋予了接口 type 和 value**

2. 尽管可以将零值赋给接口，但是接口变量 != nil。

第 2 点很容易造成问题，当将指针类型的变量传递给**形参类型为接口**的方法时，判断 nil 值很容易出现问题。这种反直觉行为的原因是，**接口本质上是**`(type, value)` 对，当你将接口和 `nil` 指针比较时，自然不相等。

解决办法：

1. 如果知道接口的 type，可以进行类型断言，再判断 nil：

```go
if i.(bool) == nil {
} 
```

2. 否则，可以通过反射：

```go
if reflect.ValueOf(i).IsNil() {
}
```

### 空接口

定义了零个方法的接口被称为空接口：`interface{}`

任何类型的变量都可以赋值给空接口，因为任何类型都实现它的全部方法**。空接口用于处理未知类型的值**。例如标准库中的 fmt. Println 方法：

```go
func Println(a ...interface{}) (n int, err error) {
    return Fprintln(os.Stdout, a...)
}
```

### 一些实践

如果一个包仅暴露一个接口，可以将此接口直接命名为 Interface：

```shell
package user
 
type Interface interface {
    GetName() string
}
```

**不要将接口的指针作为入参**，只需要使用接口本身：

```shell
func sayHelloTo(user *user.Interface) { // NOT OK
    println((*user).GetName())
}
```

要避免拷贝，只需要让**类型的指针作为接口方法的接收者**即可。

子接口类型的实参，可以传递给父接口类型的形参。所谓父子接口，就是体现在方法集的包含关系上。

## 操作符

### 自增自减

仅仅支持 i++，不支持++i。而且你不能在表达式中使用自增自减操作符：

```go
++i       // 错误
data[i++] // 错误
```

### 位操作

`^` 作为一元操作符，按位取反，作为二元操作符，异或。

```go
var a uint8 = 0x82
var b uint8 = 0x02
fmt.Printf("%08b [A]\n", a) // 10000010 [A]
fmt.Printf("%08b [B]\n", b) // 00000010 [B]
fmt.Printf("%08b (NOT B)\n", ^b) // 11111101 (NOT B)
fmt.Printf("%08b ^ %08b = %08b [B XOR 0xff]\n", b, 0xff, b^0xff) // 00000010 ^ 11111111 = 11111101 [B XOR 0xff]
fmt.Printf("%08b ^ %08b = %08b [A XOR B]\n", a, b, a^b) // 10000010 ^ 00000010 = 10000000 [A XOR B]
fmt.Printf("%08b & %08b = %08b [A AND B]\n", a, b, a&b) // 10000010 & 00000010 = 00000010 [A AND B]
fmt.Printf("%08b &^%08b = %08b [A 'AND NOT' B]\n", a, b, a&^b) // 10000010 &^00000010 = 10000000 [A 'AND NOT' B]
fmt.Printf("%08b&(^%08b)= %08b [A AND (NOT B)]\n", a, b, a&(^b)) // 10000010&(^00000010)= 10000000 [A AND (NOT B)]
```

## 类型断言

使用下面的语法可以进行**类型断言，将接口类型强制转换为真实类型**，**或者**将接口转换为**另外一个接口**：

```go
ll := LatLng{39, 100}
var gops GeoOps = &ll
// 语法：t,ok = i.(T)
llp := gops.(*LatLng)
 
type IstioObject interface { ... }
var object interface{} = ...
item, ok := object.(IstioObject)
```

如果指定两个变量来接收返回值，则：

1. 如果断言成功，t 为真实类型变量，ok 为 true

2. 如果断言失败，t 为真实类型的 nil 值，ok 为 false

如果指定单个变量来接收返回值，则断言失败会导致 panic

### switch 语法

这种语法支持在 switch 语句中进行多次断言，直到类型匹配：

```go
switch val := gops.(type) {
    case *LatLng:
        // 执行到这里则val 是 *Latlng
    default:
 
}
```

## new/make

Go 支持两种变量分配原语：new、make，二者都是内置函数。

### new

new 仅仅**用于分配内存，将其置零**，但是不初始化变量，仅仅**返回指向零值的指针**：

```go
// 为类型SyncedBuffer分配对应大小的内存并清零，然后返回一个 * SyncedBuffer
p := new(SyncedBuffer)
```

不同类型的零值具有不同含义：例如：

1. sync. Mutex 的零值表示解锁状态

2. bytes. Buffer 的零值表示空白的缓冲区。

### make

用于创建切片（slice）、映射（map）和通道（chan），并且**返回已初始化的目标类型（而非指针）**。这种设计的原因是，这三个类型必须在它们引用的数据结构被初始化后才能使用

## 空白标识符

你可以声明任何类型的空白标识符，也可以将任何表达式赋值给空白标识符。空白标识符使用符号 `_` 表示，可用来无害的丢弃某些值。

空白标识符可以用在多赋值的表达式中，忽略不关心的那些值：

```go
if _, err := os.Stat(path); os.IsNotExist(err) {
    // ...
}
```

导入包，但是仅仅执行其 init 函数而不使用其导出的成员：

```go
import _ "net/http/pprof"
```

检查是否实现了指定的接口：

```go
if _, ok := val.(json.Marshaler); ok {
    fmt.Printf("value %v of type %T implements json.Marshaler", val, val)
}
```

## 类型嵌入

Go 没有提供典型的、类型驱动的子类化机制。但是，你可以通过在结构、接口内部嵌入其它类型，来达到类似于子类化的效果。

注意类型嵌入和子类化的不同：

1. 被内嵌类型的方法成为外部类型的方法，这个行为和子类化相同

2. 当**方法被调用时，其接收者是被内嵌类型**（即方法所来自的那个类型），而不是外部类型，这个行为和子类化不同

3. 被嵌入类型，对自己被嵌入这件事情毫无感知。因此**从被嵌入类型中调用外部类型定义的方法是不可能的**

### 嵌入到接口

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
 
type Writer interface {
    Write(p []byte) (n int, err error)
}
 
// 嵌入：ReadWriter是Reader、Writer接口的联合
type ReadWriter interface {
    Reader
    Writer
}
```

注意，**只有接口才能被嵌入到接口中**。

### 嵌入到结构

```go
type Writer struct {
}
 
func (w *Writer) write() {}
 
type Reader struct {
}
 
func (r *Reader) read() {}
 
type ReaderWriter struct {
    *Writer
    *Reader
}
 
func main() {
    // 就像普通字段一样，内嵌的类型也需要初始化，如果不指定，则为零值
    rw := ReaderWriter{&Writer{}, &Reader{}}
    // 可以直接调用内嵌接口的方法
    rw.write()
    rw.read()
}
```

结构可以同时包含普通字段、内嵌结构：

```go
type Job struct {
    Command string
    *log.Logger
}
 
job := &Job{command, log.New(os.Stderr, "Job: ", log.Ldate)}
```

如果需要直接**引用内嵌类型，可以使用其类型名**：

```go
//  使用类型名引用，不要导入包前缀
job.Logger.Logf("%q: %s",...)
```

注意，结构也可以内嵌接口：

```go
type Registry struct {
    ClusterID string
    model.Controller  // 类型名Controller
}
```

**结构嵌入接口的意义是，相当于加了个“缺省适配”**，结构不必须实现任何方法，仅仅“覆盖”自己关注的方法即可。

### 引用内嵌字段

内嵌的接口、结构，可以命名，**也可以不命名（匿名内嵌）**。不命名时，直接以其类型名引用：

```go
aggregate.Registry{
    ClusterID:        clusterID,
    Controller:       kubectl,   // 直接通过类型名Controller引用
} 
```

### 名字冲突问题

嵌入可能导致接口/结构的成员名冲突（同名），Go 基于以下规则解决此冲突：

1. **越靠嵌套层次外部的成员优先级越高**。例如上面的 Job 内嵌了 Logger，如果 Logger 有一个字段 Command，则此字段被 Job. Command 隐藏

2. 同一层次下出现重名，通常是一个错误。例如上面的 Job 内嵌了 Logger，它不应该同时有一个名为 Logger 的方法或者字段。即便如此，**冲突的名称只要没有在类型定义外部被引用，就不会导致错误**

### 嵌入指针还是值

根据实际情况：

1. 如果**外层对象以值**的形式传来传去，而你需要的**内层对象的方法是定义在指针**上的，则嵌入**指针**

2. 如果**外层对象以指针**的形式传来传去，则**内层对象可以嵌入值**，没有问题，你仍然**可以访问内层对象的指针方法**

3. 如果**内层对象的方法均是值接收者，则嵌入值**

如果对象很小，可以考虑嵌入值，这样可以减少内存分配次数，并实现局部访问（内存中比较靠近）。

## 别名和类型定义

### 别名

下面的语法是定义别名，两个类型是完全一样的，可以任意替换：

```go
type nodeName = string
```

### 类型定义

下面的语法是定义一个新类型，两个类型不一样，必须强制转换：

```go
type nodeName string
 
var n nodeName = string("xenon")
str := string(n) 
```

对现有**非接口类型**进行类型定义，**新的类型不会继承原有类型的方法**：

```go
type myMutex sync.Mutex
 
var mtx myMutex
mtx.Lock() // 错误
```

要想继承，可以使用匿名嵌套：

```go
type myMutex struct {  
    sync.Mutex
}
 
var mtx myMutex
mtx.Lock() // OK
```

但是，**对于接口类型进行类型定义，方法则会被继承**。

# 流程控制

## for

Go 仅仅支持 for 这一种循环结构：

```go
// 不得使用圆括号包围（初始化语句; 条件表达式; 后置语句），但是循环体必须使用花括号包围
for i := 0; i < 10; i++ {
    fmt.Printf("%v", i)
}
// 初始化语句和后置语句是可选的
i := 0
for ; i < 10; {
    fmt.Printf("%v", i)
    i += 1
}
 
// 上段代码的前后分号可以省略，效果类似于C语言的while
i := 0
for i < 10 {
    fmt.Printf("%v", i)
    i += 1
}
 
// 死循环写法
for {
}
```

### 循环变量取地址

**不要尝试取循环变量的地址！**

在 Go 语言中，for 语句引入的变量在**整个迭代过程中是重用**的，也就是说，也就是说，每次迭代中进行取地址，**总是会指向同一个地址**：

```go
var items []NodeProblemSolverConfig
 
for idx, solverConfig := range items {
    println(&solverConfig)
    createSolver(&solverConfig)
}
 
// 打印
0xc00055eea0
0xc00055eea0
0xc00055eea0
```

在上面的例子中，期望创建出三个不同配置的 Solver 对象，而实际上它们引用的 Config 完全一致。

解决此问题的方法有两个：

1. 如果希望得到值副本的指针：

```go
copied := solverConfig
&copied
```

2. 如果希望得到原始值的指针：

```go
&solverConfig[idx] 
```

### 循环和闭包

下面的代码有问题，所有 Goroutine 打印的都是 data 最后一个元素的值：

```go
data =[]int{1,2,3,4,5} 
for _,v := range data {
    go func() {
        fmt.Println(v)  // 5 5 5 5 5
    }()
    
    go v.print()    // 同样的问题，但是更加隐蔽
}
```

解决办法是在循环体内定义局部变量：

```go
for _,v := range data {
    v := v // 每个闭包引用的不是同一变量，这种同名覆盖变量虽然奇怪，在Go里面是惯用法
    go func() {
        fmt.Println(vc)
    }()
} 
```

或者直接传参给 Goroutine：

```go
for _,v := range data {
    go func(v V) {
        fmt.Println(vc)
    }(v)
}
```

再次强调：整个循环过程中，**for 语句引入的迭代变量是的内存地址是不变的，也就是说，整个迭代过程中只有一个变量，每次都在修改同一变量的值**，在循环体内**，传递此变量的地址是危险的，特别注意闭包这种隐晦的传递地址的形式**

## if

类似于循环控制语句，分支控制语句也可以包含一个初始化语句：

```go
// 圆括号、花括号规则和for相同
// 注意可以在条件表达式之前置一个语句
if i := 0; i < 1 {
    fmt.Print(i)
}
 
i := 0;
if i < 1 {
    fmt.Print(i)
}
 
// if - else if - else 
if true {
    
} else if false {
 
} else {
 
}
```

## switch

**需要注意，Go 中 switch 语句的 case，默认行为是 break**，而其它很多语言的默认行为是进入下一个 case 继续判断、执行（注意 C 语言中，没有 break 的情况下，一旦某个 case 匹配，后续所有其它 case/default 都会执行）：

```go
// 同样可以具有前置的初始化表达式
switch os := runtime.GOOS; os {
case "darwin":
    fmt.Println("OS X")
case "linux":
    fmt.Println("Linux")
    // fallthrough则继续执行下一个分支
    fallthrough
default:
}
 
// switch也可以不带任何条件，用来编写简洁的if-then-else结构：
t := time.Now()
switch {
case t.Hour() < 12:
    fmt.Println("上午好")
case t.Hour() < 17:
    fmt.Println("下午好")
default:
    fmt.Println("晚上好")
}
 
// case合并：
switch pod.Status.Phase {
case v1.PodPending, v1.PodRunning: ...
}
```

## defer

这个关键字可以让后面的语句延迟执行：

```go
func main() {
    defer fmt.Println(" World")
    fmt.Println("Hello")
}
// Hello World
```

直到包围语句的函数（而非代码块）返回之前才执行，**因此在循环体中通过 defer 来进行清理是不可以的。解决办法是把循环体封装为函数**。

连续使用 defer 时，会形成 defer 栈，先入栈的语句后执行：

```go
func main() {
    for i := 0; i < 10; i += 1 {
        defer fmt.Print(i)   // 不能用这种方式实现每元素迭代后清理
    }
    // 9876543210
}
```

需要注意，**defer 的表达式求值发生在声明时，而非实际执行时**：

```go
var i int = 1
defer fmt.Println("result =>",func() int { return i * 2 }())  // 打印2而非4
i++ 
```

## defer 的陷阱

### defer 函数调用时机

1. 包裹 defer 的函数返回时

2. 包裹 defer 的函数执行到末尾时

3. 所在的 goroutine 发生 panic 时

### 资源清理先判断 err 再 defer

如果资源没有获取成功，即没有必要也不应该再对资源执行释放操作：

```go
resp, err := http.Get(url)
// 先判断操作是否成功
if err != nil {
    return err
}
// ...
// 如果操作成功，再进行Close操作
defer resp.Body.Close()
```

### defer 函数参数估算时机

defer 后面需要跟着函数调用，但是其**入参可以是任何表达式**。这些表达式的估算时机，是**正常执行流（非 Defer 栈）执行到 Defer 语句的**时候：

```go
var buf bytes.Buffer
Println(buf.Len())                 // 0
buf.Write(make([]byte, 10))  
Println(buf.Len())                 // 10
defer Println(buf.Len())           // 10
buf.Write(make([]byte, 10))                           
Println(buf.Len())                 // 20
```

上面的例子中， defer 语句真正执行时使用的 buf 长度，是第 3 行 write 后的长度，而非第6行。

换一种写法：

```go
var buf bytes.Buffer
Println(buf.Len())                 // 0
buf.Write(make([]byte, 10))
Println(buf.Len())                 // 10
defer func() {
    defer Println(buf.Len())       // 20
}()
buf.Write(make([]byte, 10))
Println(buf.Len())                 // 20
```

这样就可以获得最终 buf 的长度，因为对 buf.Len ()的函数调用不**会提前发生**。

### 调用 os. Exit 时 defer 不会被执行

当发生 panic 时，所在 goroutine 的所有 defer 会被执行，但是当调用 os.Exit ()方法退出程序时，defer 并不会被执行：

```go
func deferExit() {
    defer func() {
        fmt.Println("defer")
    }()
    os.Exit(0)
}
```

上面的 defer 不会执行。

## 标签

可以用于 goto 跳转、for switch、for select 跳转：

```go
loop:
    for {
        switch {
        case true:
            break loop
        }
    }
```

## goto

可以实现无条件转移：

```go
    goto label
label:
    x := 1
```

# 常用库

## 标准库列表

| 标准库 |说明 |
| ----------- |----------- |
| archive/tar |对 Tar 归档格式的支持 |
| archive/zip |对 Zip 归档格式的支持 |
| [bufio](https://blog.gmem.cc/go-io-programming) |装饰 io. Reader/io. Writer |
| bytes |操控[]byte，也就是字节切片 |
| compress/bzip2 |实现 bzip2 解压缩算法 |
| compress/flate |实现 DEFLATE 压锁算法 |
| compress/gzip |支持读写 gzip 格式 |
| compress/lzw |支持读写 lzw 格式{"userName": "Alex","userAge": 31} |
| compress/zlib |支持读写 zlib 格式 |
| container/heap |为任何实现了 heap 接口的类型提供“堆”操作。堆（heap）是实现优先级队列的一种方式 |
| container/list |实现双向链表 |
| container/ring |实现环形列表 |
| context |定义 Context 类型 |
| crypto/* |加解密相关包 |
| [database/sql](https://blog.gmem.cc/go-database-programming) |为 SQL 数据库提供一般性接口 |
| database/sql/driver |定义数据库驱动程序需要实现的接口 |
| debug/* |调试相关的包 |
| encoding |编解码（Marshaler/Unmarshaler）相关的通用接口 |
| encoding/base64 |支持 Base64 编码格式：<br>// 编码<br>base64.StdEncoding.EncodeToString ([]byte{})<br> |
| encoding/hex |支持 Hex 编码格式 |
| encoding/json |支持 JSON 格式 |
| encoding/xml |支持 XML 格式 |
| encoding/binary |在[]byte 和数字之间进行转换：<br> |
| errors |包含操控 error 的函数 |
| flag |实现命令行选项解析的功能 |
| fmt |格式化输入输出，类似于 C 语言的 printf/scanf |
| hash/* |实现散列算法 |
| html |用于处理 HTML 的转义 |
| html/template |实现事件驱动的、生成 HTML 的模板，支持防代码注入 |
| image/* |2D 图形库，支持 gif/jpeg/png 等图像格式 |
| index/suffixarray |基于内存中的 suffix array 实现对数复杂度的子串搜索 |
| [io](https://blog.gmem.cc/go-io-programming) |提供 IO 操作原语 |
| [io/ioutil](https://blog.gmem.cc/go-io-programming) |包含一些 IO 工具函数 |
| log |一个简单的日志包 |
| glog |Google 内部 C++日志框架的复制品<br> |
| log/syslog |提供访问系统日志服务的功能 |
| math |包含基本的常量和数学函数 |
| math/big |支持任意精度的算术运算 |
| math/bits |支持按位的计算 |
| math/cmplx |支持复数的计算 |
| math/rand |支持伪随机数<br> |
| mime |实现了部分 MIME 规范 |
| mine/multipart |支持 MIME Multipart 解析 |
| net |提供可移植的网络 IO 接口，包括 TCP/IP、UDP、Unix 域套接字、DNS 解析 |
| net/http |提供 HTTP 客户端和服务器实现 |
| net/http/cgi |提供 CGI 实现 |
| net/http/fcgi |实现 FastCGI 协议 |
| net/http/httptest |提供用于 HTTP 测试的工具 |
| net/http/httptrace |在 HTTP 客户端请求内部追踪事件 |
| net/http/httputil |HTTP 相关工具函数 |
| net/http/pprof |收集 HTTP 服务器运行时信息，供 pprof 分析 |
| net/mail |电子邮件解析/extend-kubernetes-with-custom-resources |
| net/smtp |SMTP 协议支持 |
| net/rpc |支持跨越网络来访问对象导出的方法 |
| net/rpc/jsonrpc |实现基于 JSON-RPC 1.0 的服务器/客户端编码方式 |
| net/url |解析 URL |
| os |平台独立的操作系统功能函数 |
| os/exec |运行外部命令 |
| os/signal |支持访问发送到当前进程的信号<br> |
| os/user |根据名称或者 ID 来查找 OS 用户 |
| path |操控基于 / 的路径 |
| path/filepath |操控文件名路径 |
| reflect |实现了运行时反射，允许程序操控任何类型的对象 |
| regexp |提供正则表达式支持 |
| runtime |包含用于和 Go 运行时系统进行交互的操作，例如控制 Goroutine |
| sort |提供排序切片或者用户定义集合的原语 |
| strconv |为基本数据类型提供到/从字符串展现的转换<br>// bool 转换为字符串<br>strconv.FormatBool (v)<br>// 字符串转换为 int<br>strconv.Atoi ("1")<br> |
| strings |提供操控基于 UTF-8 的字符串的函数 |
| sync |提供基本的同步原语，例如互斥量 |
| sync/atomic |提供低级别的原子内存原语，用于设计同步算法 |
| syscall |支持低级别的系统调用 |
| testing |用于支持 Go 包的自动化测试 |
| time |操控和访问时间 |
| unicode |提供对 Unicode 的支持 |
| unsafe |用于绕开 Go 的类型安全机制 |

## built-in

### true/false

这两个在 Go 语言中是常量，其声明如下：

```go
const (
    true  = 0 == 0
    false = 0 != 0
)
```

### iota

用在多常量声明中，其值为当前常量值的索引：

```go
const (
    x = 100
    a = iota
    b = iota
    c
    d
)
 
func main() {
    fmt.Printf("%v %v %v %v %v", x, a, b, c, d) // 100 1 2 3 4
}
```

### nil

预定义的标识符，表示指针、通道、函数、接口、映射或者切片的零值。

### append

函数签名：`func append(slice []Type, elems ...Type) []Type`

将元素附加到切片的尾部，如果切片容量足够，它被重切已满足新元素。如果容量不足，底层数组被重新分配

支持把字符串添加到[]byte 切片中：

```go
slice = append([]byte("hello "), "world")
```

### cap

函数签名：`func cap(v Type) int`

获取数组、数组指针（指向的数组）、切片、缓冲通道的容量。

### close

用于关闭通道

### complex

函数签名：`func complex(r, i FloatType) ComplexType`

用于创建复数

函数 `func imag(c ComplexType) FloatType` 返回复数的虚数部分

函数 `func real(c ComplexType) FloatType` 返回复数的实数部分

### copy

函数签名：`func copy(dst, src []Type) int`

从源切片拷贝元素到目标切片，也可以用来拷贝字符串到[]byte。返回实际拷贝的元素个数。

### delete

函数签名：`func delete(m map[Type]Type1, key Type)`

用于从映射中删除条目。

### len

函数签名：`func len(v Type) int`

返回数组、数组指针、切片、字符串（字节数）、缓冲通道的元素个数。如果 v 为 nil 则返回0

### make

函数签名：`func make(t Type, size ...IntegerType) Type`

为切片、映射、通道分配内存空间并且初始化。

### new

函数签名：`func new(Type) *Type`

为指定的类型分配内存

### panic

此内置函数用于产生一个运行时异常，通常会导致程序停止运行。此函数接收一个任意类型的参数，参数的字符串形式会被打印到控制台上。

### recover

当 panic 被调用时，不管是否显式调用，当前函数的执行会立即停止，并 Unwind 调用栈，调用 defer 函数。如果 Unwinding 到达 Goroutine 的栈顶，则程序终止。

使用内置函数 recover 可以从 Unwinding 中重新获得程序控制权，恢复正常执行流。recover 函数终止 Unwind 并捕获 panic 的参数作为返回值，它**仅仅能在 defer 函数体内调用**。

```go
type Request struct {
}
 
func (request *Request) process() {
    panic("Request process failed.")
}
 
func Worker(req *Request) {
    // 必须在defer中调用，recover类似于Java中的catch
    defer func() {
        // 如果当前没有panic，则recover返回nil
        if err := recover(); err != nil {
            fmt.Println(err)
        }
    }()
    req.process()
}
```

### print/println

内置的打印函数：

```go
func print(args ...Type)
func println(args ...Type)
```

### uintptr

一个整数类型，足够大以存放任何位模式的指针。

### error

这是一个内置的接口：

```go
type error interface {
    Error() string
}
```

fmt 包在打印时，会检测变量是否实现了该接口。

很多函数都会返回 error 值，调用者应该检查该值是否为 nil，从而判断调用是否成功：

```go
i, err := strconv.Atoi("one")
// 非零值表示调用失败
if err != nil {
    fmt.Printf("%v\n", err)
    return
}
fmt.Println(i)
```

## flag

解析命令行参数，支持短参数（-h）和长参数（--help），支持默认值、命令帮助：

```go
import "flag"
 
var (
    masterURL string
    help      bool
)
 
func main() {
    // 注册命令行参数：存放到的目标变量的指针、参数名、默认值、帮助信息
    flag.StringVar(&masterURL, "masterURL", "", "URL of kubernetes master")
    flag.Parse() // 解析
    if help {
        flag.Usage() // 打印帮助
    }
} 
```

## log

该包提供了基本的日志记录功能。示例用法：

```go
log.Printf("Process id is %v", os.Getpid())
log.Fatalf("Parent PID is %v", os.Getppid())  // 打印信息并执行panic()抛出恐慌
log.Fatal("Fatal error")                      // 打印信息并执行os.Exit(1)
```

输出内容比 fmt. Printf 多了当前时间的前缀。要配置输出格式，可以：

```go
// 前缀日期和代码位置
func init(){
    log.SetFlags(log.Ldate | log.Lshortfile)
}
// 2016/08/05 application.go:10: Process id is 10147
 
// 所有可用的标记：
const (
    Ldate = 1 << iota //日期，示例2017/01/01
    Ltime //时间,示例08:08:08
    Lmicroseconds //微秒
    Llongfile //绝对路径和行号
    Lshortfile //文件和行号
    LUTC //日期时间，UTC时间
    LstdFlags = Ldate | Ltime
)
```

## reflect

该包为 Go 语言提供了反射功能。

### 分类识别

例如判断对象是不是一个指针：

```go
if reflect.ValueOf(req).Kind() != reflect.Ptr {
  
} 
```

### 类型识别

```go
type User struct {
    Name string
    Age  uint8
}
 
var alex interface{}
alex = User{"Alex", 31}
 
// 获取任意对象的具体类型
alexType := reflect.TypeOf(alex)
intType := reflect.TypeOf(1)
fmt.Println(alexType, intType) // main.User int
```

### 值识别

Value 是一个结构，它保存任何一个对象的全部信息。

**调用 reflect.ValueOf ()**可以将任意**对象转换为 reflect. Value**

```go
alexValue := reflect.ValueOf(alex)
fmt.Printf("%T=%v\n", alexValue, alexValue) // reflect.Value={Alex 31}
 
// 下面的调用返回零值
ValueOf(nil)
```

要从 Value 获得**原始对象，可以调用 Value.Interface ()**方法：

```go
// 获得原始对象 interface{} 然后将其Cast为真实类型
alex, _ = alexValue.Interface().(User)
fmt.Printf("%T=%v\n", alex, alex)           // main.User={Alex 31} 
```

要判断一个值是否为零值，使用下面的代码：

```go
func IsZero(v reflect.Value) bool {
    // 零值是无效值，但是这样判断不足够， 还需要将  当前值      和   类型的零值    进行比较
    return !v.IsValid() || reflect.DeepEqual(v.Interface(), reflect.Zero(v.Type()).Interface())
}
```

### 获取分类

```go
fmt.Println(alexType.Kind())  // struct
fmt.Println(alexValue.Kind()) // struct
 
// 底层分类列表
const (
    Invalid Kind = iota
    Bool
    Int
    Int8
    Int16
    Int32
    Int64
    Uint
    Uint8
    Uint16
    Uint32
    Uint64
    Uintptr
    Float32
    Float64
    Complex64
    Complex128
    Array
    Chan
    Func
    Interface
    Map
    Ptr
    Slice
    String
    Struct
    UnsafePointer
)
```

### 读写字段

```go
// 遍历字段
for i := 0; i < alexType.NumField(); i++ {
    fmt.Println(alexType.Field(i).Name)
}
```

写入结构的字段：

```go
type User struct {
    Name string
    Age  uint8
}
 
alex := User{"Alex", 31}
 
// 为了修改对象的字段，必须传递指针（可寻址）
alexPtrValue := reflect.ValueOf(&alex)
 
// Elem()返回interface包含的Value、或者指针指向的Value
alexValue := alexPtrValue.Elem()
if alexValue.Kind() == reflect.Struct {
    nameField := alexValue.FieldByName("Name")
    // 可设置的前提是：字段已经导出、对象可寻址
    if nameField.CanSet() {
        nameField.SetString("Alex Wong")
    }
}
 
fmt.Println(alex) // {Alex Wong 31}
```

写入基本类型：

```go
age := 31
reflect.ValueOf(&age).Elem().SetInt(32)
fmt.Println(age) // 32
```

### 调用方法

要调用方法，首先需要获取方法的 Value 对象：

```go
alexPtrValue := reflect.ValueOf(&alex)
sayHelloMthd := alexPtrValue.MethodByName("SayHello")
```

MethodByName 根据名称来检索一个 Value（它必须是接口、结构等支持方法的对象）的方法，方法也是 Value 对象，但是它可被调用：

```go
args := []reflect.Value{
    reflect.ValueOf("Meng"),
}
 
if sayHelloMthd.IsValid() {
    rets := sayHelloMthd.Call(args)
    println ( rets[0].Interface().(string))
}
```

总之，方法、方法的参数、方法的返回值，都是 Value。Value 和实际接口/结构之间可以转换。

### 读取字段标签

```go
alex := User{"Alex", 31}
tag := reflect.TypeOf(alex).Field(0).Tag
fmt.Println(tag) // json:"userName"
```

很多现有的包都会利用字段标签，例如：

```go
json, _ := json.Marshal(alex)
fmt.Printf("%v", string(json))
// {"userName":"Alex","userAge":31}
```

## unsafe

使用该包可以绕过 Go 的内存安全机制，直接对内存进行读写。

### Sizeof

此函数返回一个类型占用的内存空间大小：

```go
fmt.Println(unsafe.Sizeof(1))        // 8
fmt.Println(unsafe.Sizeof('1'))      // 4
fmt.Println(unsafe.Sizeof("1"))      // 16
fmt.Println(unsafe.Sizeof(new(int)))    // 8
```

注意返回值仅仅和类型有关，和值没有任何关系。

### Alignof

此函数返回一个类型的对齐系数（对齐倍数）：

```go
var b bool
var i8 int8
var i16 int16
var i64 int64
var f32 float32
var s string
var m map[string]string
var p *int32
fmt.Println(unsafe.Alignof(b))       // 1
fmt.Println(unsafe.Alignof(i8))      // 1
fmt.Println(unsafe.Alignof(i16))     // 2
fmt.Println(unsafe.Alignof(i64))     // 8
fmt.Println(unsafe.Alignof(f32))     // 4
fmt.Println(unsafe.Alignof(s))       // 8      
fmt.Println(unsafe.Alignof(m))       // 8
fmt.Println(unsafe.Alignof(p))       // 8
```

对齐倍数都是 2 的幂，一般不会超过 8。你也可以基于反射来获取对齐倍数：

```go
reflect.TypeOf(x).Align()
```

### Offsetof

此函数用于获取结构中字段相对于结构起始内存地址的偏移量：

```go
type User struct {
    age  uint8
    name string
}
alex := User{}
fmt.Println(unsafe.Offsetof(alex.age))   // 0
fmt.Println(unsafe.Offsetof(alex.name))  // 8
```

你也可以基于反射来获取偏移量：

```go
reflect.TypeOf(u1).Field(i).Offset
```

### Pointer

unsafe. Pointer 是一种特殊的指针，它可以指向任何的类型，类似于 C 语言中的 void*

不同具体类型的指针是无法相互转换的：

```go
var uip *uint8 = new(uint8)
var fip *float32 = new(float32)
uip = fip  // cannot use fip (type *float32) as type *uint8 in assignment
```

但是 unsafe. Pointer 却可以和任何指针类型相互转换，包括 uintptr：

```go
var uip *uint8 = new(uint8)
var fip *float32 = new(float32)
*fip = 3.14
uip = (*uint8)(unsafe.Pointer(fip))
fmt.Println(*uip)
```

*T 不支持算术运算，但是 uintptr 却可以。利用 unsafe. Pointer 作为媒介，我们可以获得精确控制内存的能力：

```go
type User struct {
    age  uint8
    name string
}
user := new(User)
 
page := (*uint8)(unsafe.Pointer(user))
*page = 31
 
os := unsafe.Offsetof(user.name)
// 为了进行指针算术运算，必须使用uintptr
base := uintptr(unsafe.Pointer(user))
pname := (*string)(unsafe.Pointer(base + os))
*pname = "Alex"
 
fmt.Println(*user) 
```

## encoding/json

```go
// 序列化，缩进4空格
b, _ := json.Marshal(result)
var out bytes.Buffer
json.Indent(&out, b, "", "    ")
out.WriteTo(writer)
// 序列化，缩进4空格
json.MarshalIndent(data, "", " ")
```

对通过 HTTP 传递来的字节流解码为 JSON，**数字的真实类型可能是 float64**：

```go
res := make(map[string]interface{})
err = json.Unmarshal(respBytes, &res)
// id虽然是909，但是实际上是float64
id := res["id"]
task.testId = int(id.(float64)) 
```

## time

```go
n := time.Now()
// 格式化，注意layout中的时间值不能改
FMT := "2006-01-02 15:04:05"
println(n.Format(FMT))          // 2018-02-11 18:16:47
println(n.Format("2006/1/2"))   // 2018/2/11
println(n.Format("2006/01/02")) // 2018/02/11
println(n.Format("15:04:05"))   // 18:17:13
 
// 解析
n, _ = now.Parse("2018-01-02 18:16:47")
 
println(n.Format(FMT)) // 2018-01-02 00:00:00
 
// 指定解析使用的格式
time.Parse("2006-01-02T15:04:05.999999Z", str)
time.Parse("2006-01-02T15:04:05Z07:00", str)
 
// 转换为整数
println(n.Unix())               // UNIX纪元，秒
println(n.UnixNano() / 1000000) // UNIX纪元，毫秒
 
// 获取各字段 2018 January 2 18 16 47 
fmt.Printf("%v %v %v %v %v %v ", n.Year(), n.Month(), n.Day(), n.Hour(), n.Minute(), n.Second())
 
// ProtoBuf时间戳
time := protobuf.Timestamp{Seconds: n.Unix()}
```

time. Duration 表示一个时间长度：

```go
// 解析，可以使用s、m、h等单位
d, _ := time.ParseDuration("1s")
```

它的本质就是纳秒数：

```go
type Duration int64
 
const (
    Nanosecond Duration = 1
    Microsecond = 1000 * Nanosecond
    Millisecond = 1000 * Microsecond
    Second = 1000 * Millisecond
    Minute = 60 * Second
    Hour = 60 * Minute
)
```

你可以将 Duration 和整数进行算术运算：

```go
var d1 time.Duration = (60 * 1000) * time.Millisecond
var d2 time.Duration = 2 * time.Minute
```

也可以计算两个时间点之间的差距：

```go
var duration time.Duration = endingTime.Sub(startingTime)
```

## math/rand

提供伪随机数的支持。

注意随机数不随机的问题：

```go
glog.Info(rand.Intn(10))
glog.Info(rand.Intn(10))
```

以上代码无论运行多少次，都输出 1、7。原因是使用的“源”是固定的。因此，要产生每次调用都不一样的随机数，需要每次使用不同的源：

```go
source := rand.NewSource(time.Now().Unix())
gen := rand.New(source)
glog.Info(gen.Intn(1000))
 
// 或者，更简单的，你可以为默认源设置新的种子
rand.Seed(time.Now().UnixNano())
rand.Intn(100)
```

## os/exec

执行一段 Bash 脚本：

```go
ctx, _ := context.WithTimeout(context.TODO(), s.actionTimeout)
// 支持超时控制
cmd := exec.CommandContext(ctx, s.shellPath, script)
// 可以设置环境变量
cmd.Env = os.Environ()
for key, val := range vars {
    cmd.Env = append(cmd.Env, fmt.Sprintf("%s=%s", key, val))
}
// 执行
cmd.Run()
```

### 输入输出

```go
cmd := exec.Command("tr", "a-z", "A-Z")
// 提供输入
cmd.Stdin = strings.NewReader("some input")
// 指定输出缓冲
var out bytes.Buffer
cmd.Stdout = &out
err := cmd.Run()
```

也可以直接将标准输出/标准错误一起作为返回值：

```go
cmd := exec.Command("sh", "-c", "echo stdout; echo 1>&2 stderr")
// 执行并收集标准输出+标准错误
stdoutStderr, err := cmd.CombinedOutput()
// 执行并收集标准输出
stdout, err := cmd.Output()
```

### 非阻塞

要非阻塞的启动命令，但是不等待其返回，可以：

```go
cmd := exec.Command("sleep", "5")
err := cmd.Start()
```

你可以稍后等待其结束：

```go
cmd.Wait()
```

## os/signal

支持访问发送到当前进程的信号，示例代码：

```go
/* 将接收到的SIGINT、SIGTERM信号中继给chan os.Signal */
relayCh := make(chan os.Signal, 2)
signal.Notify(relayCh, os.Interrupt, syscall.SIGTERM)
go func() {
    <-relayCh
    close(stopCh) // stopCh供应用中其它协程读取
    <-relayCh
    // 再次收到信号，强制退出进程
    os.Exit(1)
}()
// 协程可以循环执行逻辑，直到stopCh关闭
wait.Until(ctrl.runWorker, time.Second, stopCh)
```

## strconv

为**基本数据类型提供到/从字符串**展现的转换，主要函数：

```go
// 将布尔值转换为字符串 true 或 false
func FormatBool(b bool) string
// 将字符串转换为布尔值
// 接受真值：1, t, T, TRUE, true, True
// 接受假值：0, f, F, FALSE, false, False
// 其它任何值都返回一个错误。
func ParseBool(str string) (bool, error)
 
// 将整数转换为字符串形式。base 表示转换进制，取值在 2 到 36 之间
// 结果中大于 10 的数字用小写字母 a - z 表示
func FormatInt(i int64, base int) string
func FormatUint(i uint64, base int) string
// 将字符串解析为整数，ParseInt 支持正负号，ParseUint 不支持正负号
// base 表示进位制（2 到 36），如果 base 为 0，则根据字符串前缀判断
// 前缀 0x 表示 16 进制，前缀 0 表示 8 进制，否则是 10 进制
// bitSize 表示结果的位宽（包括符号位），0 表示最大位宽
func ParseInt(s string, base int, bitSize int) (i int64, err error)
func ParseUint(s string, base int, bitSize int) (uint64, error)
 
// 将整数转换为十进制字符串形式。即：FormatInt(i, 10)
func Itoa(i int) string
// 将字符串转换为十进制整数。即：ParseInt(s, 10, 0)
func Atoi(s string) (int, error)
 
 
// FormatFloat 将浮点数 f 转换为字符串形式
// f：要转换的浮点数
// fmt：格式标记（b、e、E、f、g、G）
// prec：精度（数字部分的长度，不包括指数部分）
// bitSize：指定浮点类型（32:float32、64:float64），结果会据此进行舍入
//
// 格式标记：
// 'b' (-ddddp±ddd，二进制指数)
// 'e' (-d.dddde±dd，十进制指数)
// 'E' (-d.ddddE±dd，十进制指数)
// 'f' (-ddd.dddd，没有指数)
// 'g' ('e':大指数，'f':其它情况)
// 'G' ('E':大指数，'f':其它情况)
//
// 如果格式标记为 'e'，'E'和'f'，则 prec 表示小数点后的数字位数
// 如果格式标记为 'g'，'G'，则 prec 表示总的数字位数（整数部分+小数部分）
// 参考格式化输入输出中的旗标和精度说明
func FormatFloat(f float64, fmt byte, prec, bitSize int) string
// 将字符串解析为浮点数，使用 IEEE754 规范进行舍入
// bigSize 取值有 32 和 64 两种，表示转换结果的精度
// 如果有语法错误，则 err.Error = ErrSyntax
// 如果结果超出范围，则返回 ±Inf，err.Error = ErrRange
func ParseFloat(s string, bitSize int) (float64, error)
```

代码示例：

```go
strconv.ParseInt("FF", 16, 0)  // 255
strconv.ParseInt("0xFF", 0, 0) // 255
```

## encoding/binary

在[]byte 和数字之间进行转换：

```go
var a []byte = []byte{0, 1, 2, 3}
fmt.Println(binary.BigEndian.Uint32(a))
fmt.Println(binary.LittleEndian.Uint32(a))
```

## glog

Google 内部 C++日志框架的 Go 语言复制品：

1. Info/Warning/Error/Fatal 函数，以及 Infof 等格式化版本

2. V 风格的日志控制（使用命令行选项-v 和-vmodule=file=2）

代码示例：

```go
glog.Fatalf("执行失败: %s", err)
if glog.V(2) {
    glog.Info("执行成功")
}
glog.V(2).Infoln("处理了", nItems, "个条目")
```

日志被缓冲，并周期性的 Flush，程序退出前你应该手工调用 Flush。默认情况下，所有日志被写入到临时目录的文件中。

你可以通过命令行选项来改变 glog 的行为， flag. Parse 应该在任何日志打印函数调用前调用：

| 命令行选项示例 |说明 |
| ----------- |----------- |
| --logtostderr=false |日志被输出到标准错误而非文件 |
| --alsologtostderr |同时输出到文件和标准错误 |
| --stderrthreshold=ERROR |输出到标准错误的最低严重级别 |
| --log_dir="" |日志输出目录 |
| --v=0 |输出日志的最低冗余级别 |
| -vmodule=gopher*=3 |对于 gopher 开头的 Go 文件，其最低冗余级别设置为3 |

## encoding/json

支持 JSON 的串行化、反串行化。下面的代码示例将 JSON 字符串反串行化为 map：

```go
import (
    "encoding/json"
    "github.com/sanity-io/litter"
    "io/ioutil"
)
 
func main() {
    data, _ := ioutil.ReadFile("configdump.json")
    config := make(map[string]interface{})
    // 转换JSON为MAP
    json.Unmarshal(data, &config)
    litter.Dump(config)
}
```

## gopkg. in/yaml. v2

支持 YAML 格式的串行化、反串行化。下面的代码示例将 map 串行化为 YAML：

```go
import (
    "gopkg.in/yaml.v2"
    "io/ioutil"
    "os"
)
 
func main() {
    config := make(map[string]interface{})
    // 转换MAP为YAML
    data, _ := yaml.Marshal(config)
    ioutil.WriteFile("configdump.yaml", data, os.ModePerm)
}
```

## 基本工具库

### huandu/xstrings

包含各种各样的字符串处理函数。

### elliotchance/pie

专门用于切片成立，专注于类型安全、性能、不可变性。

包含一些内置的类型定义，不需要 `go generate` 即可使用：

```go
typeStrings[]string
typeFloat64s[]float64
typeInts[]int
 
 
// 示例
package main
 
import (
    "fmt"
    "strings"
 
    "github.com/elliotchance/pie/pie"
)
 
// 示例
func main() {
    name := pie.Strings{"Bob", "Sally", "John", "Jane"}.
        // 过滤
        FilterNot(func (name string) bool {
            return strings.HasPrefix(name, "J")
        }).
        // 变换
        Map(strings.ToUpper).
        // 取最后一个元素
        Last()
 
    fmt.Println(name) // "SALLY"
}
```

如果希望为任何自定义的类型生成上面这样的接口需要添加注释：

```go
type Car struct {
    Name, Color string
}
 
//go:generate pie Cars.*
type Cars []Car
```

然后执行 `go generate` 会生成 `cars_pie.go` 文件，其中定义了上面那样的接口。

你还可以自定义 Equality、Strings 等方法，实现类似于 Java 的灵活性：

```go
type Car struct {
    Name, Color string
}
 
type Cars []*Car // ElementType is *Car
 
func (c *Car) Equals(c2 *Car) bool {
    return c.Name == c2.Name
}
```

### thoas/go-funk

一个工具箱函数库，主要用于集合操作：

1. 元素存在性判断、索引判断

2. 结构转为 map，map 转为 slice

3. 任何可迭代对象的元素查找、过滤、迭代、去重

| 函数 |说明 |
| ----------- |----------- |
| Contains |检查元素是否存在（于切片、映射、数组） |
| IndexOf<br>LastIndexOf |获得元素的索引或 -1 |
| ToMap |将一个字段作为 pivot（键），转换结构为 map |
| Filter |使用 Predicate 函数来过滤切片 |
| Find |使用 Predicate 函数来查找切片元素 |
| Map |在映射、切片之间进行相互转换 |
| Get |使用路径导航的形式检索值：funk.Get (foo, "Bar. Bars. Bar. Name") |
| Keys |获取结构、映射的字段名/键数组 |
| Values |获取结构、映射的字段值、值数组 |
| ForEach |迭代映射、切片 |
| ForEachRight |反向迭代映射、切片 |
| Chunk |将切片划分为子切片，每个子切片具有指定的大小 |
| FlattenDeep |递归的扁平化多维数组 |
| Uniq |数组去重 |
| Drop |去除数组/切片的前 N 个元素 |
| Initial |获取数组/切片除后 N 个的全部元素 |
| Tail |获取数组/切片除前 N 个的全部元素 |
| Shuffle |将指定数组进行元素重排，放到新数组中 |
| Sum |数组元素求和 |
| Reverse |获取反向数组 |
| SliceOf |从单个元素创建切片 |
| RandomInt |获得一个随机整数 |
| RandomString |获得一个固定长度的随机字符串 |

## mholt/archiver

### 压缩/解压

能够快速的压缩/解压缩. zip, .tar, .tar. gz, .tar. bz2, .tar. xz, .tar. lz4, .tar. sz 等格式，还能够解压缩. rar 格式。示例：

```go
import "github.com/mholt/archiver"
 
// 压缩
err := archiver.Archive([]string{"testdata", "other/file.txt"}, "test.zip")
// 解压缩
err = archiver.Unarchive("test.tar.gz", "test")
```

上面的代码自动根据扩展名来判断使用何种归档、压缩算法。你也可以明确指定需要使用的算法：

```go
z := archiver.Zip{
    CompressionLevel:       flate.DefaultCompression,
    MkdirAll:               true,
    SelectiveCompression:   true,
    ContinueOnError:        false,
    OverwriteExisting:      false,
    ImplicitTopLevelFolder: false,
}
 
err := z.Archive([]string{"testdata", "other/file.txt"}, "/Users/matt/Desktop/test.zip")
```

### 探看文件

下面是探看 Zip 中文件条目的例子：

```go
err = z.Walk("/Users/matt/Desktop/test.zip", func(f archiver.File) error {
    zfh, ok := f.Header.(zip.FileHeader)
    if ok {
        fmt.Println("Filename:", zfh.Name)
    }
    return nil
})
```

### 写入 HTTP 响应

```go
err = z.Create(responseWriter)
if err != nil {
    return err
}
defer z.Close()
 
for _, fname := range filenames {
    info, err := os.Stat(fname)
    if err != nil {
        return err
    }
    
    // 获取文件在归档文件中的内部名称
    internalName, err := archiver.NameInArchive(info, fname, fname)
    if err != nil {
        return err
    }
 
    // 打开文件
    file, err := os.Open(f)
    if err != nil {
        return err
    }
 
    // 写入到文件
    err = z.Write(archiver.File{
        FileInfo: archiver.FileInfo{
            FileInfo:   info,
            CustomName: internalName,
        },
        ReadCloser: file,
    })
    file.Close()
    if err != nil {
        return err
    }
}
```

## archive/tar

处理 Tar 格式的归档，写示例：

```go
var buf bytes.Buffer
var chart []byte = []byte{1, 2, 3}
w := tar.NewWriter(&buf)
// 写入条目
// 写入头
w.WriteHeader(&tar.Header{
    Name: "Chart.yaml",
    Size: int64(len(chart)),
})
// 写入体
w.Write(chart)
// 刷出
w.Close()
// 写到文件
ioutil.WriteFile("/tmp/chart.tar", buf.Bytes(), fileutil.PrivateFileMode)
```

读示例：

```go
tr := tar.NewReader(buf)
// 处理下一个条目
header, err := tr.Next()
println( header.Name )
buf := make([]byte, header.Size)
// 读取此条目的内容到缓冲区
tr.Read(buf)
```

## compress/gzip

处理 Gzip 压缩格式，下面是写 tar. gz（tgz）的例子：

```go
var buf bytes.Buffer
gw := gzip.NewWriter(&buf)
// 装饰
tw := tar.NewWriter(gw)
 
yamlBytes := []byte("AppVersion: 1.0.0")
tw.WriteHeader(&tar.Header{
    Name: "Chart.yaml",
    Size: int64(len(yamlBytes)),
    Mode: 0666,
})
tw.Write(yamlBytes)
tw.Close()
gw.Close()
 
ioutil.WriteFile("/tmp/chart.tgz", buf.Bytes(), 0666) 
```

下面是读的例子：

```go
gzipBuf := bytes.NewBuffer(req.Zip)
gr, err := gzip.NewReader(gzipBuf)
if err != nil {
    // 说明是无效GZIP格式
}
defer gr.Close()
tr := tar.NewReader(gr)
```

## 数据绑定/拷贝

### mitchellh/mapstructure

支持将一般性的 Map 解码为 Go 结构，或者反向转换。示例：

```go
item := make(map[string]interface{}]
//...
// 创建一个解码器
decoder, _ := mapstructure.NewDecoder(&mapstructure.DecoderConfig{
    Metadata: nil,
    Result:   &chart,
    // 定制类型转换器
    DecodeHook: func(from reflect.Type, to reflect.Type, v interface{}) (interface{}, error) {
        if to.String() == "*timestamp.Timestamp" {
            return parseTime(v.(string)), nil
        }
        // 不转换，直接返回原始值
        return v, nil
    },
})
// 执行解码
decoder.Decode(item)
```

### mitchellh/copystructure

深拷贝（DeepCopy）对象，不管是 map、slice、pointer 都能递归的解引用并正确拷贝。示例：

```go
cs, _ := copystructure.Copy(allChartsProto)
```

### imdario/mergo

用于合并结构、映射。非导出字段不会被合并，导出字段则会被递归的合并：

```go
// 合并
if err := mergo.Merge(&dst, src); err != nil {
}
// 将映射合并到结构
if err := mergo.Map(&dst, srcMap); err != nil {
}
// 也可以将映射合并到映射，一定要记得对映射取地址（Why？本身就是传递引用）
mergo.Map(&dstMap, srcMap)
```

需要注意的是：将结构合并到映射时，不会递归合并。

你还可以提供转换器，指定特定类型字段在合并之前如何转换：

```go
import (
    "fmt"
    "github.com/imdario/mergo"
        "reflect"
        "time"
)
 
type transfomer struct {
}
 
func (t timeTransfomer) Transformer(typ reflect.Type) func(dst, src reflect.Value) error {
       // 时间类型的转换器
    if typ == reflect.TypeOf(time.Time{}) {
        return func(dst, src reflect.Value) error {
            if dst.CanSet() {
                                // 判断是否零值的方法
                isZero := dst.MethodByName("IsZero")
                result := isZero.Call([]reflect.Value{})
                if result[0].Bool() {
                    dst.Set(src)
                }
            }
            return nil
        }
    }
    return nil
}
 
type Snapshot struct {
    Time time.Time
}
 
func main() {
    src := Snapshot{time.Now()}
    dest := Snapshot{}
    mergo.Merge(&dest, src, mergo.WithTransformers(transfomer{}))
    fmt.Println(dest)
}
```

# 和 C/C++交互

## 对 C/C++的支持

你可以导入伪包“C”来使用 C 语言中定义的类型，此导入前面紧跟着的注释叫做 Preamble。Preamble 中可以包含任意 C 代码，包括函数、变量声明和定义：

```c
package main
 
/*
typedef int (*accumulator) (int a, int b);
 
int add(int a, int b){
    return a+b;
}
int reduce(accumulator acc,int a,int b){
    return acc(a,b);
}
*/
// 伪包C
import "C"
import "fmt"
import "unsafe"
 
func main() {
    // 通过伪包C来调用C。C.accumulator获取reduce的函数指针
    f := C.accumulator(C.add)
    // 调用C函数
    fmt.Printf("%v", C.reduce(f, 1, 2)) // 3
    
    // 创建一个C字符串（char*）
    cs := C.CString("Hello World")
    // 释放C内存
    C.free(unsafe.Pointer(cs))
    
    // 将C分配的内存授予Go指针
    p := (*int)(C.malloc(4))
    // 释放C分配的内存
    C.free(unsafe.Pointer(p))
}
```

在构建时，一旦 go 发现 `import "C"` 语句，就会寻找目录中的非 go 源码。c/s/S 扩展名的文件会使用 C 编译器编译，cc/cpp/cxx 文件会使用 C++编译器编译。

CFLAGS, CPPFLAGS, CXXFLAGS, FFLAGS, LDFLAGS 等环境变量可以通过伪指令 #cgo来定义 ：

```go
// #cgo CFLAGS: -DPNG_DEBUG=1
// #cgo amd64 386 CFLAGS: -DX86=1
// #cgo LDFLAGS: -lpng
// #include <png.h>
import "C"
 
 
// 通过pkg-config获得CPPFLAGS和LDFLAGS，默认pkg-config工具可以通过PKG_CONFIG获得
// #cgo pkg-config: png cairo
// #include <png.h>
import "C"
```

在构建时，CGO_CFLAGS, CGO_CPPFLAGS, CGO_CXXFLAGS, CGO_FFLAGS, CGO_LDFLAGS 等环境变量被附加到上述指令引入的环境变量之后，构成构建 C 代码所需要的完整变量。特定于包的标记应该通过伪指令而非环境变量设置。

包中所有 CPPFLAGS、CFLAGS 指令用于编译包中的 C 源码，所有 CPPFLAGS、CXXFLAGS 指令则用于编译 C++源码。

程序中所有包的 LDFLAGS 指令会被连接在一起，并在链接阶段使用。

`#cgo` 伪指令中可以包括一些变量，${SRCDIR}会被替换为源码目录的绝对路径：

```go
// #cgo LDFLAGS: -L${SRCDIR}/libs -lfoo
```

### cgo

这是 Go 提供的用于支持 C/C++的工具。在进行本地编译时，该工具默认启用；在进行交叉编译时，该工具默认禁用。

设置环境变量 CGO_ENABLED 可以控制是否使用 cgo，设置为 1 启用，设置为 0禁用。

## Go 调用 C

在 Go 文件中，如果需要访问的 C 结构字段名属于 Go 关键字，可以前缀一个 `_` 来访问。C 结构中无法在 Go 语言中解释的字段被忽略（例如 bitfield）。

### 类型映射关系

标准的 C 数字类型映射到的 Go 类型如下表：

| C 类型 |Go 类型 |备注 |
| ----------- |----------- |----------- |
| char |C.char | |
| signed char |C.schar | |
| unsigned char |C.uchar | |
| short |C.short | |
| unsigned short |C.ushort | |
| int |C.int | |
| unsigned int |C.uint | |
| long |C.long | |
| unsigned long |C.ulong | |
| long long |C.longlong |/extend-kubernetes-with-custom-resources |
| unsigned long long |C.ulonglong | |
| float |C.float | |
| double |C.double | |
| complex float |C.complexfloat | |
| complex double |C.complexdouble | |
| void* |unsafe. Pointer |任何指针都可以转换为 unsafe. Pointer，unsafe. Pointer 也可以转换为任何类型的指针 |
| __int128_t__uint128_t |[16]byte | |
| char* |C.CString | |
此外需要注意：

1. 如果需要直接访问 C 的 struct、union、enum 类型，为类型名字前缀 struct_、union_、enum_

2. 要获得任何 C 类型的尺寸，调用C.sizeof_T

3. 由于 Go 不支持 C 的联合体，因此联合体在 Go 中映射为等长度的字节数组

4. Go 的结构不能包含 C 类型的内嵌字段

5. cgo 把 C 类型转换为非导出的 Go 类型，因此任何 Go 包都不能在其导出 API 中暴露 C 类型。一个包中的 C 类型，和另一个包中的同名 C 类型是不一样的

6. 在多赋值上下文中，可以调用任何 C 函数，第一个变量赋值为 C 函数返回值，第二个变量赋值为 C 函数调用的 errno

7. 目前不支持调用 C 函数指针，但是你可以定义 Go 变量来引用函数指针。C 函数可以调用这种来自 Go 的函数指针

8. C 的函数，如果参数是定长数组，实参传递指向某个数组的首元素指针。在 Go 中调用这类函数时，你需要明确的传递数组首元素的指针：`C.f(&C.x[0])`

9. 调用C.malloc 时，不会直接调用 C 库中的 malloc，二是调用 Go 对 malloc 的包装函数。此包装函数保证C.malloc 调用不会返回 nil。如果内存不足，程序直接崩溃

### 链接到 C 库

可以仅仅在 Go 代码中声明 C 头文件，然后再链接到对应的 C 库：

```go
// 在当前目录下寻找libxxx进行链接
// #cgo LDFLAGS: -L ./ -lxxx
// #include "xxx.h"
import "C"
 
func main() {
    C.xxx()
}
```

## C 调用 Go

使用注释来标注某个函数可以导出供 C 使用：

```go
//export MyFunction
func MyFunction(arg1, arg2 int, arg3 string) int64 {...}
 
//export MyFunction2
func MyFunction2(arg1, arg2 int, arg3 string) (int64, *C.char) {...}
```

在头文件_cgo_export. h 中，可以看到如下形式的声明：

```c
extern int64 MyFunction(int arg1, int arg2, GoString arg3);
// 多返回值函数，其返回值被包装为结构体
extern struct MyFunction2_return MyFunction2(int arg1, int arg2, GoString arg3);
```

## 指针的传递

Go 是支持垃圾回收的语言，它需要知道每个指向 Go 内存的指针的具体位置，因此，在 C 和 Go 代码之间传递指针受到限制。

如果一个 Go 指针指向的内存不包含任何 Go 指针，则该 Go 指针可以传递给 C 代码。

C 代码不应该存储任何 Go 指针（因为其由 Go 的垃圾回收器管理）。当将结构的某个字段的指针传递给 C 时，存在问题的是结构的某个字段；当将 Go 数组、切片的指针传递给 C 时，存在问题的是整个数组或切片。在**调用完毕后，C 代码不应该保留任何 Go 指针**。

被 C 代码调用的 Go 函数，不得返回 Go 指针。但是这种函数可以：

1. 将 C 指针作为入参，这些指针可以指向非指针、C 指针，但是不能指向 Go 指针

2. 将 Go 指针作为入参，但是这些指针所指向的内存，不得包含 Go 指针

Go 代码不得在 C 内存中存储 Go 指针。C 代码则可以在 C 内存中存储 Go 指针。但是在 C 函数返回时，必须立即停止存储 Go 指针。

上述规则可以在运行时动态检查。配置环境变量 GODEBUG，设置其值为：

1. cgocheck=1 执行廉价的动态检查

2. cgocheck=0 禁用动态检查

3. cgocheck=2 执行完整的检查，需要消耗一定的资源

要打破上述规则，可以使用 unsafe 包。另外，没有任何机制来阻止 C 代码做违反上述规则的事情。如果打破上述规则，程序很可能意外的崩溃。

# 常用命令

## go

这是一个用于管理 go 源代码的工具。

注意，某些子命令的行为会受到 `GO111MODULE` 环境变量的影响。

| 子命令 |说明 |
| ----------- |----------- |
| build |编译包及其依赖，但是不安装编译结果<br>当编译单个 main 包时，结果二进制文件的 basename 默认和第一个源文件（go build a.go b.go 输出a.exe）或者目录（go build unix/sam 输出 sam. exe）相同<br>当编译多个包或者单个非 main 包时，编译结果被丢弃，也就是仅仅验证编译是否可以通过<br>当编译包时，_test. go 结尾的文件被忽略<br>**格式：**<br>go build [-o output] [-i] [build flags] [packages]<br>**选项：**<br>-o  仅编译单个包时可用，显式指定输出的二进制文件或对象文件的名字<br>-i 安装编译目标的依赖<br>-a 强制重新构建所有包，即使已经 up-to-date<br>-v 编译后打印被编译包的名称<br>-buildmode mode 构建模式<br>-compiler name 使用的编译器，gccgo gc<br>-gccgoflags 'arg list' 编译器 gccgo 的标记<br>-gcflags 'arg list'  编译器 gc 的标记，参考 go tool compile -help<br>-installsuffix suffix 包安装路径名后缀<br>-ldflags 'flag list'  链接标记，参考 go tool link -help<br>-linkshared 与共享库进行链接，共享库使用 -buildmode=shared 创建<br>-pkgdir dir  从 dir 安装、加载所有包，而非默认位置<br>**构建模式：**<br>-buildmode=archive  构建非 main 包为. a 文件，main 包被忽略<br>-buildmode=c-archive  构建 main 包以及所有它导入的包为 C 归档文件<br>-buildmode=c-shared 构建 main 包以及所有它导入的包为 C 共享库<br>-buildmode=shared 构建非 main 包为共享库，main 包被忽略<br>-buildmode=exe 构建 main 包以及所有它导入的包为可执行文件<br>-buildmode=pie  构建为位置独立可执行文件（PIE）<br>-buildmode=plugin 构建为 Go 插件<br>**示例：**<br>export GOROOT=/home/alex/Go/sdk/1.9.2<br>export GOPATH=/home/alex/Go/workspaces/default<br>pushd /home/alex/Go/workspaces/default/src/digital-app &gt; /dev/null<br>export PATH=/home/alex/Go/sdk/1.9.2/bin/:$PATH<br> <br># 构建<br>go build -i -o build/digitalsrv<br> <br># 静态连接<br># CGO_ENABLED 提示使用 cgo 而非 go 作为编译器，以便进行静态连接<br># -a 重新构建所有依赖<br># -ldflags '-s'减小二进制文件大小<br>CGO_ENABLED=0 go build -i -a -o build/digitalsrv -ldflags '-s'<br> |
| tool compile |将单个 Go 包编译为对象文件<br>**格式：**go tool compile [flags] file...<br>**选项：**<br>-I dir1 -I dir2 导入包的搜索路径，在搜索 $GOROOT/pkg/$ GOOS_$GOARCH 之后搜索<br>-N 禁用优化<br>-c int 编译时的并发度，默认 1<br>-dynlink 允许引用共享库中的 Go 符号<br>-l 禁用内联<br>-lang version 设置 Go 语言版本，例如-lang=go1.12<br>-largemodel 基于大内存模型的假设来编译<br>-pack 生成归档而非对象文件<br>-shared 生成能够链接到共享库的代码<br>-dwarf 生成 DWARF 符号<br> |
| tool link |从 main 包中读取 Go 归档/object，以及它们的依赖，并链接为二进制文件<br>**格式：**<br>go tool link [flags] main. a<br> <br># 从 go build 调用<br>go build -ldflags "[flags]"<br>**选项：**<br>-D address 设置数据段地址<br>-T address 设置文本段地址<br>-E entry 设置入口符号名称<br>-H type 设置可执行文件格式类型，默认类型从 GOOS+GOARCH 推断<br>-I interpreter 设置使用的 ELF 动态链接器<br>-L dir1 -L dir2 在搜索 $GOROOT/pkg/$ GOOS_$GOARCH之后，从dir1 dir2中搜索导入包<br>-X importpath.name=value设置导入路径中的字符串变量name的值为value。示例：<br>-X github.com/stefanprodan/flagger/pkg/version.REVISION=$ {GIT_COMMIT}"<br>上述选项只有当源码中定义了 REVISION 变量且它的值没有初始化、或者初始化为常量字符串表达式时有意义：<br>package version<br> <br>var VERSION = "0.8.0"<br>var REVISION = "unknown"<br>-a 反汇编输出<br>-buildid id 设置 Go 工具链的 build id<br>-buildmode mode 构建模式，默认 exe<br>-c Dump 出调用图<br>-compressdwarf 如果可能压缩 DWARF，默认 true<br>-cpuprofile file 将 CPU profile 写入到文件<br>-d 禁止生成动态可执行文件。控制是否生成动态头。默认情况下即使没有引用任何动态库也会生成动态头<br>-dumpdep Dump 出符号依赖图<br>-extar ar 设置外部归档程序（默认 ar），仅配合-buildmode=c-archive 使用<br>-extld linker 设置外部链接器（默认 clang 或 gcc）<br>-extldflags flags 空格分隔的，传递给外部链接器的选项<br>-installsuffix suffix 从 $GOROOT/pkg/$ GOOS_$GOARCH_suffix 中搜索包，而非 $GOROOT/pkg/$ GOOS_$GOARCH<br>-linkmode mode 设置链接模式，可选值 internal, external, auto<br>-linkshared 和已经安装的 Go 共享库进行链接<br>-msan 支持 C/C++内存消毒器<br>-n Dump 出符号表<br>-o 将输出写入到指定文件<br>-r dir1: dir2... 设置 ELF 动态链接器的搜索路径<br>-race 链接到竞态检测共享库<br>-s 移除符号表和调试信息<br>-shared 生成共享对象，隐含-linkmode external<br>-w 禁止 DWARF 生成<br>**链接模式：**<br>internal：解析并链接主机上的对象文件（ELF/Mach-O/PE ...）到最终可执行文件里面。由于实现宿主机链接器的全部功能存在困难，因此这种模式下能够链接的对象文件种类是受限的<br>external：为了支持链接到任何对象文件而不需要动态库，cgo 支持所谓外部链接模式。此模式下，所有 Go 代码被收集到 go. o 文件中，并通过宿主机链接器（通常 gcc）将 go. o 以及所有依赖的非 Go 的代码链接到最终可执行文件里面<br>大部分构建同时编译代码、调用链接器来创建二进制文件。在使用 cgo 的情况下，编译时期已经依赖 gcc，因此链接阶段再次依赖 gcc 没有问题<br> |
| clean |移除编译后的对象文件（Object files）<br>**格式：**<br>go clean [-i] [-r] [-n] [-x] [build flags] [packages]<br> |
| doc |显示包或者符号的文档<br>**格式：**<br>go doc [-u] [-c] [package|[package.]symbol[. methodOrField]]<br> <br># 显示 fmt 包的 Errorf 函数的文档<br> go doc fmt Errorf<br> |
| env |打印 Go 相关环境变量信息，相关的环境变量包括：<br>GOOS，目标操作系统，支持 darwin、freebsd、linux、windows、android 等<br>GOARCH，目标体系结构，支持 arm、arm64、386、amd64 等<br> |
| fix |针对指定的包运行 Go fix 命令。Fix 能够查找到使用旧 API 的 Go 程序，并将其替换为新 API。升级到新版本的 Go 之后，可以调用此命令<br> |
| fmt |指定指定的包源代码运行 gofmt |
| generate |通过处理源文件，产生 Go 文件 |
| get |下载并安装包及其依赖<br>**格式：**go get [-d] [-f] [-fix] [-insecure] [-t] [-u] [build flags] [packages]<br>**选项：**<br>-d 仅仅下载，不安装<br>-f 配合-u，不去检查是否每个 import 语句对应的包以及从它的原始代码仓库检出<br>-fix  在解析依赖、构建代码之前，对下载的包运行 fix tool<br>-t 同时下载构建测试代码所需的依赖<br>-insecure 允许通过非安全连接下载代码<br>-u 使用网络下载包及其依赖<br>-v 显示详细输出<br> |
| install |编译并安装包及其依赖 |
| list |列出包 |
| run |编译并运行程序 |
| test |测试指定的包 |
| tool |运行指定的 Go tool |

# 依赖管理

## vendor

go vendor 是 go 1.5 官方引入依赖包管理机制。其基本思路是，**将引用的外部包的源代码放在当前工程的 vendor 目录下面**，go 1.6 以后编译 go 代码**会优先从 vendor 目录先寻找依赖包**。这样，当前工程**放到任何机器的$GOPATH/src 下都可以通过编译**。

如果不使用 vendor 机制，每次构建都需要 go get 依赖包，**下载的依赖包版本可能和工程需要的版本不一致**，导致编译问题。

仅可执行程序（main 包）应该 vendor 依赖，共享的库则不应该。当你编写项目时，应该将**所有传递性的依赖都扁平化到当前项目的 vendor 目录下**。

依赖搜索的优先级：**vendor 目录 ⇨ $GOROOT ⇨ $GOPATH**

## glide

go vendor 无**法精确的引用外部包进行版本控制**，不能指定引用某个特定版本的外部包。只是在开发时，将其拷贝过来，但是一旦外部包升级，vendor 下的代码不会跟着升级，而且 vendor 下面并没有元文件记录引用包的版本信息，这个引用外部包升级产生很大的问题，无法评估升级带来的风险。

glide 是 Go 语言的包管理工具，它能够管理 vendor 目录。

### 安装

```shell
curl https://glide.sh/get | sh
```

### 命令

格式：`glide [global options] command [command options] [arguments...]`

常用子命令：

| 子命令 |说明 |
| ----------- |----------- |
| create |别名 init，创建一个新工程，包含 glide. yaml 文件 |
| cw |config-wizard，自动扫描代码中的依赖，给出依赖版本的建议 |
| get |下载一或多个包到 vendor 目录，并在 glide. yaml 中添加依赖项 |
| rm |从 glide. yaml 中移除依赖，重新生成 lock 文件 |
| import |从其它依赖管理系统中导入文件 |
| nv |列出目录下所有 non-vendor 路径，如果不希望测试依赖的包，可以：<br>go test $(glide novendor)<br> |
| install |安装项目的依赖，读取 glide. lock 文件，根据其中的 commit id 来安装特定版本的包<br>如果 glide. lock 文件不存在，则此命令自动调用 glide update 并生成 glide. lock<br> |
| update |更新项目的依赖 |
| list |列出所有的依赖项 |
| cc |清除 Glide 缓存 |

## dep

Go 语言的官方的试验阶段的依赖管理工具。需要 Go 1.9+版本。从 1.11 版本开始，Go 工具链内置了和 dep 差异很大的依赖管理机制。

### 安装

```shell
go get -u github. com/golang/dep/cmd/dep
```

### 创建项目

```shell
mkdir -p $GOPATH/src/github. com/gmemcc/dep-study
cd $GOPATH/src/github. com/gmemcc/dep-study
 
dep init # 自动生成 Gopkg. toml Gopkg. lock vendor/
```

### 管理依赖

dep ensure 可用于管理依赖，更新依赖后，vendor 目录中的内容出现变化。dep ensure 的功能包括：

1. 添加新的依赖

2. 更新现有依赖

3. 对 Gopkg. toml 中的规则变化作出响应

4. 项目中第一次导入某个包，或者移除某个包的最后一个导入后，作出响应

命令示例：

```shell
# 添加依赖
dep ensure -add github. com/foo/bar github. com/baz/quux
# 更新一个依赖项目到新版本
dep ensure -update github. com/foo/bar
# 更新所有依赖，通常不建议
# 搜索匹配 Gopkg. toml 中的 branch、version、revision 约束的代码版本
dep ensure -update
```

### Gopkg. toml

此文件由 dep init 自动生成，后续主要由人工编辑。此文件可以包含几种规则声明，以控制 dep 的行为：

| 规则类型 |说明 |
| ----------- |----------- |
| constraints |定义直接依赖如何加入到依赖图中：<br> [[constraint]] <br># 导入路径<br>&nbsp;&nbsp; name = "github. com/user/project"<br># 导入的版本，可以指定 version branch 或 revision<br># version 操作符：<br># = 等于 ！= 不等于 &gt; 大于 &lt; 小于 &gt;= 大于等于 &lt;= 小于等于<br># - 版本范围，示例 1.2 - 1.4.5<br># ~ 小版本范围，示例 ~1.2.3 表示大于等于 1.2.3 但小于 1.3.0<br># ^ 大版本范围，示例 ^1.2.3 表示大于等于 1.2.3 但小于 2.0.0<br># xX* 通配符，示例&nbsp;&nbsp; 1.2. x 表示大于等于 1.2.0 但小于 1.3.0<br>&nbsp;&nbsp; version = "1.0.0"<br>&nbsp;&nbsp; branch = "master"<br># 通常是反模式，例如 Git 的 SHA1<br>&nbsp;&nbsp; revision = "abc123"<br> <br># 可选。此依赖的源码的替换位置（URL 或者导入路径），通常用在 fork 的场景下<br>&nbsp;&nbsp; source = "<https://github.> com/myfork/package. git"<br>&nbsp;&nbsp;[metadata]<br># 定义一些键值对，dep 本身不使用这些键值对<br> |
| overrides |覆盖所有依赖（直接或传递）的规则，应当小心使用。示例：<br> [[override]] <br>&nbsp;&nbsp; name = "k8s. io/api"<br>&nbsp;&nbsp; version = "kubernetes-1.11.0"<br> <br> [[override]] <br>&nbsp;&nbsp; name = "k8s. io/apimachinery"<br>&nbsp;&nbsp; version = "kubernetes-1.11.0"<br> <br> [[override]] <br>&nbsp;&nbsp; name = "k8s. io/code-generator"<br>&nbsp;&nbsp; version = "kubernetes-1.11.0"<br> <br> [[override]] <br>&nbsp;&nbsp; name = "k8s. io/client-go"<br>&nbsp;&nbsp; version = "kubernetes-1.11.0" <br> |
| required |必须在任何 [[constraint]] 或 [[override]] 之前声明<br>dep 通过分析 go 代码中的 import 语句来构建一个依赖图。required/ignored 规则用于操控此依赖图<br>required 列出必须包含在 Gopkg. lock 中的包的列表，此列表会和当前项目导入的包合并。required 可以**强制声明一个未 import 的包为项目的直接依赖**<br>对于 linter、generator 或者其它开发工具，如果：<br>1. 被当前项目使用<br>2. 不被直接或传递的导入到当前项目<br>3. 你不需要把这些工具加入 GOPATH，或者你想锁定版本<br>则可以使用 required 规则声明：<br>required = ["k8s. io/code-generator/cmd/client-gen"]<br> |
| ignored |必须在任何 [[constraint]] 或 [[override]] 之前声明<br>dep 通过分析 go 代码中的 import 语句来构建一个依赖图。required/ignored 规则用于操控此依赖图<br>required 列出 dep 进行静态代码分析时需要忽略的包，可以使用通配符：<br>ignored = ["github. com/user/project/badpkg*"]<br> |
| metadata |定义供第三方工具使用的元数据，可以定义在根下，或者 constraint、override 下 |
| prune |定义全局/某个项目的依赖修剪选项。这些选项决定了写入 vendor 时哪些文件被忽略<br>支持以下变量，取值均为布尔：<br>1. unused-packages 提示不在包导入图中出现的目录，应该被修剪掉<br>2. non-go 提示不被 Go 使用的文件应该修剪掉<br>3. go-tests 将 Go 测试文件修剪掉<br>dep init 会自动生成：<br>[prune]<br>&nbsp;&nbsp; go-tests = true<br>&nbsp;&nbsp; unused-packages = true<br>你可以为每个项目（依赖）定义修剪规则：<br>[prune]<br>&nbsp;&nbsp; non-go = true<br> <br>&nbsp;&nbsp; [[prune.project]] <br>&nbsp;&nbsp;&nbsp;&nbsp; name = "github. com/project/name"<br>&nbsp;&nbsp;&nbsp;&nbsp; go-tests = true<br>&nbsp;&nbsp;&nbsp;&nbsp; non-go = false<br> |

## vgo

由于 Go 一直没有提供官方的依赖管理工具，导致了 dep，glide，govendor，godep 等工具群雄争霸的局面。vgo 是解决这种现状的早期尝试，vgo 即 Versioned go。

### 安装

```shell
go get -u golang. org/x/vgo 
```

### go. mod

这是 vgo 的依赖配置文件，需要放在项目的根目录下。

### 命令

vgo 具有同名的 CLI，可以使用的子命令包括：

| 子命令 |说明 |
| ----------- |----------- |
| install |安装依赖 |
| build |编译项目 |
| run |运行项目 |
| get github. com/pkg |获取依赖的最新版本。依赖包数据会缓存到 GOPATH 路径下的 src/mod 目录<br> |
| get github. com/ pkg@v1.0 |获取依赖的指定版本 |
| mod -vendor |将依赖直接放在 vendor 目录中 |

## go modules

从 vgo 发展而来。

Go modules 在 1.11 属于试验特性，环境变量`GO111MODULE`用于控制其开关，取值 auto/on/off，默认 auto。

从 1.16 开始，默认开启 go modules，即使项目中没有 go. mod 文件。计划在 1.17 中完全废弃对 GOPATH 模式的支持。

### 工作流

使用 go modules 进行依赖管理的日常工作流如下：

1. 在 Go 源码中，使用 import 语句导入需要的包

2. 调用 go build/test 等命令时，会自动将依赖加入到 go. mod 并下载依赖

3. 如果需要限定依赖的版本，你可以选用以下方法之一：

    1. 使用 go get，例如：

```shell
go get foo@v1.2.3
go get foo@master
go get foo@e3702bed2 
```

    2. 手工修改 go. mod 文件

### 代理

很多 Google 的库在国内无法访问，可以设置环境变量：

```shell
# 默认源
export GOPROXY= https://goproxy.io
 
# 国内源
export GOPROXY= https://goproxy.cn
```

这样，所有模块都会通过此代理下载。

### 访问私有仓库

你需要在仓库软件管理页面创建自己的 Access Token，并配置 Git：

```shell
#                                用户 Token
git config --global url." https://alex:***@git.pacloud.io".insteadOf " https://git.pacloud.io"
 
# SSH 方式
git config --global url." git@git.tencent.com : ". insteadOf " https://git.tencent.com/"
```

然后，需要设置跳过代理：

```shell
export GOPRIVATE=*. pacloud. io,*. gmem. cc
```

### 命令

Go modules 在`go mod`下定义了若干子命令：

| 子命令 |说明 |
| ----------- |----------- |
| init |初始化一个新模块，示例：<br>cd gotools<br>go mod init github. com/gmemcc/gotools<br> |
| download |将模块下载到本地缓存 |
| edit |编辑 go. mod |
| graph |打印模块依赖图 |
| tidy |添加缺失的、移除多余的模块 |
| vendor |将依赖的副本拷贝到 vendor 目录 |
| verify |验证依赖是否满足期望 |
| why |解释包或模块为何被 main 模块需要：<br>go mod why github. com/coreos/etcd<br> |
此外，你还可能用到以下 Go 命令：

```shell
# 显示构建时会实际使用的直接、间接依赖的版本
go list -m all
 
# 显示所有直接、间接依赖可用的 minor/patch 版本更新
go list -u -m all
 
# 列出依赖的可用版本
go list -m -json -versions go. etcd. io/ etcd@latest
 
# 更新所有直接、间接依赖到最新的 minor 版本
go get -u
# 更新指定的依赖
go get -u -v go. etcd. io/etcd
# 更新所有直接、间接依赖到最新的 patch 版本
go get -u=patch
 
# 在模块根目录下执行，构建或测试模块的所有包
go build ./...
go test ./...
 
# 修剪不再需要的依赖
go mod tidy
 
# 创建并同步到 vendor 目录
go mod vendor
```

### go. mod

仅仅包含四个指令：

| 指令 |说明 |
| ----------- |----------- |
| module |声明当前模块的标识：module github. com/my/thing<br>此标识提供了模块路径（module path），模块中所有包的导入路径，都以此模块路径为前缀。模块路径 + 包相对于 go. mod 的路径共同决定了包的导入路径<br> |
| require |声明依赖：<br>require (<br>&nbsp;&nbsp;&nbsp;&nbsp; github. com/some/dependency v1.2.3<br>&nbsp;&nbsp;&nbsp;&nbsp; github. com/another/dependency/v4 v4.0.0<br>)<br> |
| replace |仅仅针对当前（主）模块，在构建主模块时，非主模块的 go. mod 中 replace 声明被忽略。该指令可以：<br>1. 用来映射导入路径（在你 Fork 一个项目并做补丁的情况下使用）：<br># 模块代码中使用的导入路径&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 实际寻找代码时的导入路径<br>replace example. com/original/import/path =&gt; /your/forked/import/path<br>2. 精确的控制依赖的版本：<br>replace example. com/some/dependency =&gt; example. com/some/dependency v1.2.3<br>3. 提示一个多模块协作项目的模块位于磁盘的绝对、相对路径：<br>module example. com/me/hello<br> <br>require (<br>&nbsp;&nbsp; example. com/me/goodbye v0.0.0<br>)<br> <br>replace git. yun. gmem. cc/eks/chart-service =&gt; ../chart-service<br>这种用法可以将依赖位置定位到磁盘，解除对 VCS 的依赖<br> |
| exclude |仅仅针对当前（主）模块，在构建主模块时，非主模块的 go. mod 中 exclude 声明被忽略<br>该指令用于禁止某个直接或传递性的依赖包，例如：<br>exclude k8s. io/client-go v2.0.0-alpha. 0.0.20190313235726-6ee68ca5fd83+incompatible<br> |

### 模块

一个 Go 项目通常会在 Git 上托管源码，例如 github. com/gmem/gotools，项目的代码库的 URL 称为代码库（Repo）。

在同一 Repo 中，可以有多个包（Package），在 Go 1.11 中这些包被**抽象为模块（Module）—— 模块是一系列相关包的集合**，使用**go. mod**来记录模块的元数据。

Google 的风格是使用单个代码库（Mono Repo），包括像 Istio 这样的项目，所有组件都在同一个代码库中。这种情况下，在单个代码库中创建多个模块是自然的需求，Go modules 支持这种需求：

```shell
# 单代码库单模块
monorepo-single-mod
├── go. mod
├── pkga
├── pkgb
└── pkgc
 
# 单代码库多模块
monorepo-multi-mods
├── proj1
│   ├── go. mod
│   ├── pkga
│   ├── pkgb
│   └── pkgc
├── proj2
│   ├── go. mod
│   ├── pkga
│   ├── pkgb
│   └── pkgc
└── proj3
    ├── go. mod
    ├── pkga
    ├── pkgb
    └── pkgc
```

逐级的 1: N 关系：**Repo ⇨ Module ⇨ Package ⇨ Source**。

### 依赖搜索模式

我们最熟悉的依赖搜索模式：vender、$GOPATH 下搜索，叫做 GOPATH mode。从 1.8 开始，可以不去显式的设置 GOPATH，SDK 默认使用~/go

Go modules 引入一种新的 Module-aware mode，在此模式下：

1. 你的项目源码不需要放在 GOPATH 下

2. 源码树的顶层目录下有一个 go. mod 文件，这种文件定义了一个 Go 模块

3. 放置了 go. mod 的目录称为**模块根目录，它通常是源码库的根目录，但非必须**

4. 模块根目录，及其子目录中的所有 Go 包，都属于模块。**除了那些自身定义了 go. mod 的子目录**

5. Go 编译器不再在 vendor、GOPATH 下寻找第三方 Go 包，而是会直接去下载并编译，然后更新 go.mod：

```go
module proj1
 
// 分析出的依赖，放到 require 区域
require (
    # 使用最新版的代码，并以 Pseudo-versions 形式记录
    github. com/gmem/gotools/x v0.0.0-20190515063616-861b08fcd24b
    github. com/gmem/gotools/y v0.0.0-20190515005150-3e3f9af80a02 // indirect   传递性依赖
) 
```

6. Go 编译器会把下载的依赖包，缓存到`$GOPATH/pkg/mod`目录下

### 依赖版本选择

go. mod 一旦被创建，其内容将被 Go 工具链管理，执行 get/build/mod 都会导致 go. mod 被修改。

命令`go list -m`输出的信息被称为 b**uild list，它是构建当前 module 所要构建的所有相关 Package（及其版本）的列表**：

```json
// go list -m -json all
{
    "Path": "proj1",
    "Main": true,
    "Dir": "/root/proj1"
}
{
    "Path": "github. com/gmem/gotools/x",
    "Version": "v0.0.0-20190515063616-861b08fcd24b",
    "Time": "2015-05-15T06:36:16Z",
    "Dir": "/home/alex/Go/workspaces/default/pkg/mod/github. com/gmem/gotools/ x@v0.0.0-20190515063616-861b08fcd24b "
}
```

标记为 Main: true 的模块，称为主模块（main module），即执行 Go 命令所在的目录。Go 会在当前目录，以及当前目录的祖先目录寻找 go. mod 文件。

如果依赖打了发布版的 Tag，则不会使用最新版的代码的 Pseudo-version，而是**使用最新的发布版**。

如果需要**显式依赖指定版本**，可以使用命令来操控 go. mod：

```shell
go mod -require=github. com/gmem/gotools/ x@v1.0.0
go mod -require=github. com/gmem/gotools/ y@v1.1.0
```

上述命令修改 go. mod：

```shell
require (
    github. com/gmem/gotools/x v1.0.0 // indirect
    github. com/gmem/gotools/y v1.1.0 // indirect
)
```

你还可以**指定依赖表达式**：

```shell
go mod -require='github. com/gmem/gotools/y@>=v1.1.0'
```

使用 go get 命令，可以更新 go. mod 中的依赖为指定分支的最新 Commit：

```shell
go get -u git. pacloud. io/pks/ helm-operator@master 
```

### 版本号格式

发布版 Tag 必须是`vMAJOR. MINOR. PATCH`格式。

`incompatible`表示你的依赖不支持 Go modeules。

Pseudo-version 的格式是`v0.0.0-yyyymmddhhmmss-abcdefabcdef`。

### 版本号升级

对于一个库 helm. sh/helm，Tag 1.x.x 发布后，消费者这样引用：

```shell
require helm. sh/helm v1.0.0 
```

没有问题。但是如果主版本改成 3.x.x，引用：

```shell
require helm. sh/helm v3.0.2 
```

就会提示：require helm. sh/helm: version "v3.0.2" invalid: module contains a go. mod file, so major version must be compatible: should be v0 or v1, not v3

解决办法是，对于 3. x 版本，（库的作者）修改模块路径，添加一个 v3 后缀：

```shell
# Git 仓库地址 https://github.com/helm/helm 一直不变
module helm. sh/helm/v3
```

相应的，消费者这样引用：`require helm. sh/helm/v3 v3.0.2`

如果你想 Fork 此库到 Git 私服 git. pacloud. io/pks/helm，然后 Replace 时必须这样写：

```shell
#                                          注意这个 v3 不能少，否则报错
# replace git. pacloud. io/pks/helm: version "v3.0.2" invalid: module contains a go. mod file, 
# so major version must be compatible: should be v0 or v1, not v3
helm. sh/helm/v3 => git. pacloud. io/pks/helm/v3 v3.0.2
```

### 同步到 vendor

使用下面的命令，可以将某个模块的全部依赖保存一份副本到根目录的 vendor 目录：

```shell
go mod vendor
```

这样，你就可以使用 vendor 下的包来构建当前模块了：

```shell
go build -getmode=vendor tool. go
```

生成的 vendor 还可以用来兼容 1.11 以前的版本，但是由于 1.11 以前的版本**不支持在 GOPATH 之外使用 vendor 机制**，因此你需要把代码库移动到$GOPATH/src 下。

### vendor 模式

Go modules 支持使用 vendoring 模式：

```shell
# 使用环境变量
export GOFLAGS=-mod=vendor
 
# 或者
go build -mod=vendor ...
```

上述命令提示 Go 命令**使用 main 模块的 vendor 目录来满足依赖，而忽略 go. mod 中的指令**。

# 最佳实践

## 代码组织

Go 代码包含在工作区中：

1. 你通常把所有的 Go 代码存放在单一的工作区中

2. 一个工作区中可能包含多个版本控制仓库

3. 每个仓库中可能包含一个或者多个包

4. 每个包会包含一个或者多个**位于单个目录中的 Go 源码文件**

5. 到达包的目录路径，对应了它的导入路径

一个工作区包含以下目录：

1. src 目录包含源代码。其中常常包含多个版本库。源代码文件必须总**是以 UTF-8 方式存储**

2. pkg 目录包含包对象

3. bin 目录可执行文件

### GOPATH

该环境变量指定你的工作区的位置，默认指向$HOME/go

### 导入路径

导入路径唯一的识别包，一个包的导入路径对应了它在工作区中、或者远程仓库中的位置。

标准库占用了很多简短的导入路径，例如 fmt、net/http。设计自己的包时，你要注意避免导入路径的冲突。例如，可以使用你的 GitHub 账号作为包路径前缀。

## 命名

### 驼峰式记法

变量命名使用驼峰式大小写。

### Getter/Setter

不提供 Getter/Setter 支持，如果你有一个字段 owner，建议`Owner ()`方法作为 Getter，`SetOwner ()`作为 Setter。

### 接口名

一般只有一个方法的接口，以`er`作为后缀，例如 Reader、Writer、 Formatter、CloseNotifier。

### 包名

包名应该是导入路径的**最后一段**：位于 src/encoding/base64 的包，其导入路径为 encoding/base64，而其包名是 base64。

包命名方面没有硬性规定。但是作为惯例，包名应该尽可能简短、仅仅使用小写字母。

包名仅仅用于导入，不需要是全局唯一的，如果出现名字冲突，你可以在导入时赋予别名：

```go
//     别名包名
import FMT "fmt"
 
func main () {
    FMT.Print (0)
}
```

## 工程布局

Go 官方没有提供开发工程的结构的标准布局，下面是 Go 生态系统中常用的一种布局：

/cmd 存放工程会产生的所有二进制文件

/binname/main. go 二进制文件 binname 的入口点函数，量尽量少的胶水代码

/internal 存放工程私有的、不需要被其它应用程序或者库引用的 Go 代码

/app  私有应用程序代码

/pkg 可以被私有应用程序共享的库代码

/pkg  可以被外部应用导入、使用的库代码

/vendor  依赖的外部库代码

/api  OpenAPI/Swagger Specs、JSON Schema 文件、Protocol 定义文件

/web  Web 应用的特殊组件，例如静态资产、服务器端模板

/configs  配置文件模板、默认配置

/init 系统初始化（Systemd、System V 等）配置、进程管理器（Supervisor）配置

/scripts  执行构建、安装、分析等任务的脚本

/build  打包和 CI 用目录

/package  云/容器/操作系统级别的打包配置信息

/ci  持续集成配置信息

/deployments  IaaS/PaaS/容器编排的配置文件，例如 K8S 资源定义，或者 Chart

/test 额外的外部测试应用，以及测试数据

/docs 设计文档、用户手册

/tools  工程的支持性工具

/examples  示例代码

/assets  资产文件，例如图片、图标

/githooks  Git 的钩子

## 测试

Go 提供了一个轻量级的测试框架，此框架由`test`包和`go test`命令组成。

要编写测试用例，你需要创建一个以`_test. go`结尾的源文件。该文件中包含名为`func TestXXX (t *testing. T)`的函数。

注意：运行一个测试文件时，里面的**所有函数在一个进程内**调用完成。

## 远程包

包的导入路径可以用来描述如何从某个版本控制系统（Git 或者 Mercurial）获取包的源代码。go 命令会自动基于此路径从远程仓库自动抓取包（**及其依赖的其它包**）：

```shell
go get github. com/golang/example/hello
```

如果上述包不存在于本地工作区，则 go 命令会自动下载到工作区。如果上述包已经存在，则 go get 的行为和 go install 相同。

## Getter/Setter

Go 没有提供统一的 Getter/Setter 支持，参考如下方式：

```go
type User struct {
    // 字段小写，表示不导出到包外部
    name string
}
 
// Getter，不需要 Get 前缀
func (user *User) Name () string {
    return user. name
}
 
// Setter，通常使用 Set 前缀
func (user *User) SetName (name string) {
    user. name = name
}
```

## 接口名称

作为惯例，单方法的接口，命名使用-er 后缀，例如 Reader、Writer、Formatter。

## 驼峰式大小写

如果一个标识符包含多个单词，Go 中惯例的方式是使用 CamelCase 或者 camelCase 风格的驼峰式大小写，而不是使用下划线。

## 暴露接口而非实现

如果某个类型仅仅是为了实现一个接口，则应该导出接口，而非类型本身。然后使用构造函数创建类型的实例：

```go
// 接口
type Block interface {
    BlockSize () int
    Encrypt (src, dst []byte)
    Decrypt (src, dst []byte)
}
 
type Stream interface {
    XORKeyStream (dst, src []byte)
}
 
// 构造函数
func NewCTR (block Block, iv []byte) Streamgcflags
 
// 实现 ...
```

## 内存

Go 语言使用垃圾回收机制，这可能导致性能问题。要编写高性能的应用程序，应当尽量避免触发 GC。

### 内存重用和对象池

利用 sync. Pool，预先分配好一块内存，然后反复的使用它。这样不但 GC 压力小，而且 CPU 缓存命中更高、TLB 效率更高，代码的速度可能提升10倍。

### 避免使用指针

引用“外部”对象具有与生俱来的开销：

1. 指向对象需要 8 字节

2. 每个独立对象具有隐藏的内存开销，可能在 8-16 字节之间

3. 使用引用（指针）效率更低，因为 GC 写屏障的存在

因此，如果不需要共享 OtherStuff，使用下面的风格：

```go
type MyContainer struct {
  inlineStruct OtherStuff
}
```

而非：

```go
type MyContainer struct {
  outoflineStruct *OtherStruct
}
```

### 不要提供需要内存分配的 API

倾向于：

```go
// 由调用者提供缓冲
func (r *Reader) Read (buf []byte) (int, error)
```

而不是：

```go
func (r *Reader) Read () ([]byte, error)
```

后者可能导致大量内存分配。

### 关于 Goroutine

1. Goroutine**基本上只有栈的开销，一开始栈只有 2KB**

2. 尽管 Goroutine 成本较低，还是要避免在主要的请求处理路径上创建 Goroutine。提前创建 Goroutine 并让其等待输入

## ToString

为任何命名类型实现如下签名的方法，即可获得类似 Java 的 toString ()的功能：

```go
package main
import "fmt"
 
type bin int
func (b bin) String () string {
        return fmt.Sprintf ("%b", b)
}
 
func main () {
        fmt.Println (bin (42))  // 101010
}
```

# 远程调试

## 构建选项

为了保留调试信息，你需要在构建时设置特定的标记：

```shell
go build -i -a -o build/eb-rdbg -gcflags="all=-N -l" -ldflags='-linkmode internal'
                                -gcflags="-N -l"  # 1.10 之前的版本，用于保留调试信息
```

## 运行应用程序

你需要通过 dlv 来启动被调试应用程序，dlv 具有反复重启被调试程序的能力：

```shell
# 需要预先安装 dlv
go get -u github. com/derekparker/delve/cmd/dlv
# 启动应用程序
dlv --listen=: 2345 --headless=true --api-version=2 exec build/eb-rdbg
```

## 连接到应用程序

你需要连接到被调试应用程序：

```shell
dlv connect localhost:2345
```

如果使用 IDE，则更加简单。例如对于 Goland，你只需要创建一个 Go Remote 类型的 Run Configuration 即可。

## 调试命令

连接到被调试应用程序后，你会看到`(dlv)`命令提示符，以下命令可用：

|   |命令 |说明 |
| ----------- |----------- |----------- |
| h |help |显示命令列表，或者显示某个命令的帮助：<br># 显示命令列表<br>help<br># 显示 config 命令的帮助信息<br>help config <br> |
|  |config |进行 dlv 配置：<br># 列出配置<br>config -list<br> <br># 保存位置到磁盘，覆盖当前配置文件<br>config -save<br>示例：<br># 进行源码路径映射<br>config substitute-path /go/src /home/alex/Go/workspaces/default/src <br> |
| b |break |设置断点，格式：break [name] &lt; linespec&gt;<br>其中 linespec 格式可以是：<br>1. *&lt; address&gt;，内存地址<br>2. &lt; filename&gt;:&lt; line&gt;，文件路径和行号，文件路径可以是部分路径，甚至仅仅是 basename，只要不产生歧义即可<br>3. +&lt; offset&gt;，当前行的后面 N 行<br>4. -&lt; offset&gt;，当前行的前面 N 行<br>5. &lt; function&gt;[:&lt; line&gt;]，指定函数的第 N 行，函数的完整形式&lt; package&gt;. (*&lt; receiver type&gt;).&lt; function name&gt;，但是仅仅 function name 是必须的，其它的可以省略（只要不产生歧义）<br>6. /&lt; regex&gt;/，匹配正则式的位置<br>示例：<br>b pkg/cmd/cli/restic/server. go:156 <br> |
| cond |condition |设置条件式断点：condition &lt; breakpoint name or id&gt; &lt; boolean expression&gt; |
| bp |breakpoints |列出断点 |
|  |clear |删除一个断点：clear &lt; breakpoint name or id&gt; |
|  |clearall |删除全部断点 |
|  |on |到达断点时执行命令：on &lt; breakpoint name or id&gt; &lt; command&gt;，可用命令 print, stack, goroutine |
| c |continue |执行到下一个断点，或者程序结束 |
|  |locals |打印本地变量：[goroutine &lt; n&gt;] [frame &lt; m&gt;] locals [-v] [&lt; regex&gt;] |
|  |vars |打印包变量：vars [-v] [&lt; regex&gt;] |
|  |print |估算一个表达式的值：[goroutine &lt; n&gt;] [frame &lt; m&gt;] print &lt; expression&gt; |
|  |whatis |打印表达式的类型：whatis &lt; expression&gt; |
|  |set |设置变量的值：[goroutine &lt; n&gt;] [frame &lt; m&gt;] set &lt; variable&gt; = &lt; value&gt; |
| n |next |Step Over：next [count]，count 指定前进多少行 |
| s |step |单步跟踪，遇到函数会自动 Step Into |
| so |stepout |Step Out |
| bt |stack |打印当前调用栈 |
|  |up |向上移动帧，可以同时指定在其上执行命令：up [&lt; m&gt;] &lt; command&gt; |
|  |down |向下移动帧，可以同时指定在其上执行命令：down [&lt; m&gt;] &lt; command&gt; |
|  |frame |设置当前帧，或者在一个指定的帧上执行命令 ：frame &lt; m&gt; &lt; command&gt; |
| l |list |列出当前调用栈对应的源码<br>也可以指定列出任何协程的源码：[goroutine &lt; n&gt;] [frame &lt; m&gt;] list [&lt; linespec&gt;]<br> |
|  |funcs |列出函数，一般都要带正则式，否则太多了： |
| gr |goroutine |显示协程，或者切换当前协程：goroutine &lt; id&gt; &lt; command&gt; |
| grs |goroutines |列出所有协程 |
|  |libraries |列出加载的动态库 |
|  |sources |列出所有源文件清单 |
|  |restart |重启被调试进程，dlv 进程保持不变 |
| q |exit |退出调试客户端 |
|  |deferred |在延迟调用上下文中执行命令 |
|  |args |打印程序参数 |
|  |disassemble |执行反汇编 |
|  |regs |打印寄存器内容 |

# 常见问题

## 零散问题

### 编译报错 cannot load... but does not contain package

使用命令`go mod tidy`可以发现根源。

### 调用包内函数提示 undefiend

原因是构建时没有引用目标函数所在文件，可以这样进行构建：

```shell
go build *. go
```

### 容器内报错 no such file or directory

找不到可执行文件：could not launch process: fork/exec /eb-rdbg: no such file or directory

实际上此文件是存在的，只是二进制格式不支持。CGO_ENABLED=0 后发现解决。

### go get 私有仓库报错 disabled by GOPRIVATE/GONOPROXY

命令：`go get -u git. pacloud. io/pks/ addons-api-server@develop`

报错原因：可能因为执行命令时 Gitlab 正在处理 develop 分支的 Push

### checksum mismatch

基于 Go Modules 进行构建，或者执行命令`go mod tiny`可能产生此错误。可能需要清除 mod 缓存重新下载：

```shell
go clean -modcache
cd project && rm go. sum
go mod tidy
```
