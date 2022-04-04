## 变量

在初始化之前，**局部变量**是*未定义的(undefined)*。

- 定义变量：
```Java
int a = 1;

int a = 0, b = 1;

int a, b;
a = 0;
b = 1;

// 注释
/* 注释 */
/** 文档注释 */
```

- 具名常量：
```Java
final int MAX = 50;
```
`final` 变量的值一旦初始化后就不能再变化，它是不可变的(immutable)。

## 数据类型

### 基础类型

| type | size |
|---|---|
|boolean|true/false|
|char|16 Bit|
|byte|8 Bit|
|short|16 Bit|
|int|32 Bit|
|long|64 Bit|
|float|32 Bit|
|double|64 Bit|

### 数据结构

- 枚举 `enum Suit {CLUBS, DIAMONDS, HEARTS, SPADES}`

## 控制流
  
### 循环
```Java
for(int i = 0; i <= 5; i++) {
	System.out.println(i);
}

int j = 0;
while(j <= 5) {
	System.out.println(j);
	j++;
}
```
`for`循环可以在初始化部分声明循环变量。

```Java
i++;
// 等价于
i = i + 1;
// 或者
i += 1;
```

###  条件
```Java
if() {

}
else {

}
```


## 类和对象


