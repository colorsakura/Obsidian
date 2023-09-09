---
layout: note
Date: 2023-02-15 15:02
Tag: TODO, lua
---

## 基本语法

### 注释

- 单行注释：`-- 单行注释`
- 多行注释：`--[[ 多行注释 ]]--`

### 关键词

以下列出了 lua 的保留关键词. 保留关键字不能作为常量或变量或其他用户自定义标示符：

TODO

### 变量

在默认情况下, lua 中的变量总是认为是<mark style="background: #FF5582A6;">全局的</mark>. 

全局变量不需要声明, 给一个变量赋值后即创建了这个全局变量，访问一个没有初始化的全局变量也不会出错，只不过得到的结果是：`nil`. 

如果你想删除一个全局变量, 只需要将变量赋值为 `nil`. 

## 数据类型

Lua 是动态类型语言, 变量不要类型定义,只需要为变量赋值.  值可以存储在变量中，作为参数传递或结果返回。

Lua 中有 8 个基本类型分别为：nil、boolean、number、string、userdata、function、thread 和 table. 

| 数据类型 | 描述                                                                                                 |
|:-------- |:---------------------------------------------------------------------------------------------------- |
| nil      | 这个最简单, 只有值nil属于该类，表示一个无效值（在条件表达式中相当于false）.                          |
| boolean  | false, true                                                                                          |
| number   | 表示双精度类型的实浮点数                                                                             |
| string   | 字符串由一对双引号或单引号来表示                                                                     |
| function | 由 C 或 Lua 编写的函数                                                                               |
| userdata | 表示任意存储在变量中的C数据结构                                                                      |
| thread   | 表示执行的独立线路, 用于执行协同程序                                                                 |
| table    | Lua 中的表（table）其实是一个"关联数组"（associative arrays）, 数组的索引可以是数字、字符串或表类型. |

在 lua 里, table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表.

## 变量

变量在使用前, 需要在代码中进行声明，即创建该变量. 

编译程序执行代码之前编译器需要知道如何给语句变量开辟存储区, 用于存储变量的值. 

Lua 变量有三种类型：全局变量、局部变量、表中的域. 

Lua 中的变量全是全局变量, 哪怕是语句块或是函数里，除非用 local 显式声明为局部变量. 

局部变量的作用域为从声明位置开始到所在语句块结束. 

变量的默认值均为 nil. 

### 赋值语句

赋值是改变一个变量的值和改变表域的最基本的方法. 
```lua
a = "hello" .. "world"
t.n = t.n + 1
```

Lua 可以对多个变量同时赋值, 变量列表和值列表的各个元素用逗号分开，赋值语句右边的值会依次赋给左边的变量. 
```lua
a, b = 10, 2*x       <-->       a=10; b=2*x
```

遇到赋值语句Lua会先计算右边所有的值然后再执行赋值操作, 所以我们可以这样进行交换变量的值：
```lua
x, y = y, x                     -- swap 'x' for 'y'
a[i], a[j] = a[j], a[i]         -- swap 'a[i]' for 'a[j]'
```

当变量个数和值的个数不一致时, Lua会一直以变量个数为基础采取以下策略：
	a. 变量个数 > 值的个数             按变量个数补足nil
	b. 变量个数 < 值的个数             多余的值会被忽略

## 循环语句

| 循环类型     | 描述                                                                            |
| ------------ | ------------------------------------------------------------------------------- |
| while        | 在条件为 true 时, 让程序重复地执行某些语句. 执行语句前会先检查条件是否为 true。 |
| for          | 重复执行指定语句, 重复次数可在 for 语句中控制.                                  |
| repeat until | 重复执行循环, 直到 指定的条件为真时为止                                         |
### while 循环

```lua
while(condition)
do
   statements
end
```

```lua
a=10  
while( a < 20 )  
do  
   print("a 的值为:", a)  
   a = a+1  
end
```

### for 循环

-   **数值for循环：**
```lua
for var=exp1,exp2,exp3 do  
    <执行体>  
end  
```
var 从 exp1 变化到 exp2, 每次变化以 exp3 为步长递增 var，并执行一次 **"执行体"**. exp3 是可选的，如果不指定，默认为1。
```lua
for i=1,f(x) do  
    print(i)  
end  
   
for i=10,1,-1 do  
    print(i)  
end
```
for的三个表达式在循环开始前一次性求值, 以后不再进行求值. 比如上面的f(x)只会在循环开始前执行一次，其结果用在后面的循环中。

验证如下:
```lua
#!/usr/local/bin/lua    
function f(x)    
    print("function")    
    return x*2    
end    
for i=1,f(5) do print(i)    
end
```
以上输出结果为：
```
function
1
2
3
4
5
6
7
8
9
10
```

-   **泛型for循环:**
泛型 for 循环通过一个迭代器函数来遍历所有值, 类似 java 中的 foreach 语句. 

Lua 编程语言中泛型 for 循环语法格式:
```lua
--打印数组a的所有值  
a = {"one", "two", "three"}
for i, v in ipairs(a) do
    print(i, v)
end 
```

i是数组索引值, v是对应索引的数组元素值. ipairs是Lua提供的一个迭代器函数，用来迭代数组。

循环数组 days：
```lua
#!/usr/local/bin/lua    
days = {"Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"}    
for i,v in ipairs(days) do  print(v) end    
```

以上实例输出结果为：
```
Sunday
Monday
Tuesday
Wednesday
Thursday
Friday
Saturday
```

### repeat

Lua 编程语言中 repeat...until 循环语句不同于 for 和 while循环, for 和 while 循环的条件语句在当前循环执行开始时判断,
而 repeat...until 循环的条件语句在当前循环结束后判断.

```lua
repeat
   statements
until( condition )
```

```lua
--[ 变量定义 --]  
a = 10  
--[ 执行循环 --]  
repeat  
   print("a的值为:", a)  
   a = a + 1  
until( a > 15 )
```

### 循环控制

- break
退出当前循环或语句, 并开始脚本执行紧接着的语句. 

- goto
将程序的控制点转移到一个标签处. `:: Label ::`
```lua
local a = 1  
::label:: print("--- goto label ---")  
  
a = a+1  
if a < 3 then  
    goto label   -- a 小于 3 的时候跳转到标签 label  
end
```


## 流程控制

Lua 编程语言流程控制语句通过程序设定一个或多个条件语句来设定. 在条件为 true 时执行指定程序代码, 在条件为 false 时执行其他指定代码。

```lua
--[ 0 为 true ]  
if(0)  
then  
    print("0 为 true")  
end
```

```lua
--[ 定义变量 --]  
a = 100;  
--[ 检查条件 --]  
if( a < 20 )  
then  
   --[ if 条件为 true 时执行该语句块 --]  
   print("a 小于 20" )  
else  
   --[ if 条件为 false 时执行该语句块 --]  
   print("a 大于 20" )  
end  
print("a 的值为 :", a)
```

```lua
--[ 定义变量 --]  
a = 100  
  
--[ 检查布尔条件 --]  
if( a == 10 )  
then  
   --[ 如果条件为 true 打印以下信息 --]  
   print("a 的值为 10" )  
elseif( a == 20 )  
then    
   --[ if else if 条件为 true 时打印以下信息 --]  
   print("a 的值为 20" )  
elseif( a == 30 )  
then  
   --[ if else if condition 条件为 true 时打印以下信息 --]  
   print("a 的值为 30" )  
else  
   --[ 以上条件语句没有一个为 true 时打印以下信息 --]  
   print("没有匹配 a 的值" )  
end  
print("a 的真实值为: ", a )
```


## 函数

在 lua 中, 函数是对语句和表达式进行抽象的主要方法. 既可以用来处理一些特殊的工作，也可以用来计算一些值. 

Lua 提供了许多的内建函数, 你可以很方便的在程序中调用它们，如print()函数可以将传入的参数打印在控制台上. 

Lua 函数主要有两种用途：
-   1.完成指定的任务, 这种情况下函数作为调用语句使用；
-   2.计算并返回值, 这种情况下函数作为赋值语句的表达式使用. 

Lua 编程语言函数定义格式如下：

```lua
optional_function_scope function function_name( argument1, argument2, argument3..., argumentn)
    function_body
    return result_params_comma_separated
end
```
解析：
-   **optional_function_scope:** 该参数是可选的制定函数是全局函数还是局部函数, 未设置该参数默认为全局函数, 如果你需要设置函数为局部函数需要使用关键字 **local**. 
-   **function_name:** 指定函数名称.
-   **argument1, argument2, argument3..., argumentn:** 函数参数, 多个参数以逗号隔开，函数也可以不带参数. 
-   **function_body:** 函数体, 函数中需要执行的代码语句块. 
-   **result_params_comma_separated:** 函数返回值, Lua语言函数可以返回多个值，每个值以逗号隔开. 

## 
