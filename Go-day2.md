

# GO 语言基础

## 数组

Go 语言的数组是一个**定长(一经创建，长度固定不可修改)**的序列，其中元素类型相同。创建数组：

```go
[length]Type
[N]Type{val1,val2...valN}
[...]Type{val1,val2...valN}     // ... 操作符，Go 语言会自动计算数组的长度
```

数组的长度 `len()` 函数获得，数组的容量 `cap()` 函数，两者返回的值相同

使用操作符 `[]` 来索引，也可以使用 `range` 进行索引访问。

### 数组切片

数组是定长的，按值传递；而切片是引用类型，可以调整长度，传递切片的成本非常小

常见切片的语法：

```go
make([]Type, length, capacity)  // 切片的长度为 capacity
make([]Type, length)            // 切片的长度为 length
[]Type{}
[]Type{val1,val2,...,valN}
```

内置函数 make() 用于创建切片、映射和通道。创建一个隐藏的初始化为零值的数组，然后返回一个引用该隐藏数组的切片。该隐藏数组也是定长的，而切片的容量即为隐藏数组的长度，而其长度为不超过其容量的任意值

```go
package main

import (
    "fmt"
)

func main() {
    a := [...]int{1, 2, 3, 4, 5, 6, 7}     // 创建数组 [1,2,3,4,5,6,7]
    fmt.Printf("len and cap of array %v is: %d and %d\n", a, len(a), cap(a))
    fmt.Printf("item in array: %v is:", a)
    for _, value := range a {
        fmt.Printf("% d", value)
    }

    fmt.Println()

    s1 := a[3:6]                           // 以数组 a 为基础创建切片对象
    fmt.Printf("len and cap of slice: %v is: %d and %d\n", s1, len(s1), cap(s1))
    fmt.Printf("item in slice: %v is:", s1)
    for _, value := range s1 {
        fmt.Printf("% d", value)
    }

    fmt.Println()

    s1[0] = 456                            // 切片对象的值改变，会导致其引用的数组对应的值发生改变
    fmt.Printf("item in array changed after changing slice: %v is:", s1)
    for _, value := range a {
        fmt.Printf("% d", value)
    }

    fmt.Println()

    s2 := make([]int, 10, 20)             // 创建全新的切片对象，自动初始化为 0 
    s2[4] = 5
    fmt.Printf("len and cap of slice: %v is: %d and %d\n", s2, len(s2), cap(s2))
    fmt.Printf("item in slice %v is:", s2)
    for _, value := range s2 {
        fmt.Printf("% d", value)
    }

    fmt.Println()
}
```





## 包

包是各种类型和函数的集合。在包中，如果标示符的首字母是大写，那这些标示符是可以被导出的，在包以外使用。

当使用 import 关键字导入包时，Go语言会在 $GOPATH ,GOROOT 目录中搜索包。





# 控制流程

Go 语言提供的流程控制语句包括 `if`、`switch`、`for`、`goto`、`select`，其中 `select` 用于监听 `channel`（通道）

if 语句语法：

```go
if optionalStatement;booleanExpression1 {            // optionalStatement 是可选的表达式
	block1
} else if optionalStatement2;booleanExpression2 {    // booleanExpression 真正决定分支的走向
    block2
} else {
    block3
}
```



for 语句语法：

```go
for {                                 // 无限循环
	block
}

for booleanExpression {               // while 循环，Go 语言中没有 while 关键字
    block
}

for index, char := range aString {    //迭代字符串
    block
}

for item := range aChannel {          // 迭代通道
    block
}
```

跳转语句，跳转到本函数内的某个标签

```go
func myfunc() {
	i := 0
	THIS:                // 定义一个 THIS 标签
	i++
	if i < 1 {
		goto THIS        // 跳转到 THIS　标签
	}
}
```



switch 分支语句

```go
switch optionalStatement; optionalExpression {
	case expression1:block1
	...
	case expressionN:blockN
	default: blockD
}
```



# 函数

特殊的函数 main ，必须出现在 main 包里，且只能出现一次。当 Go 程序运行时，会自动调用 `main` 函数开始整个程序的执行。`main` 函数不可接收任何参数，也不返回任何结果。

函数的组成：关键字 func，函数名，参数列表，返回值，函数体，返回语句

```go
package Add

func add(a int, b int) (num int) {
    return a + b
}
```

函数的调用：

```go
package main
import {
	"Add",
	"fmt"
}

func main(){
	c := Add.add(1, 2)                  // 调用上述Add包中的add函数
	fmt.Println(c)
}
```

Go语言中，可以有多个返回值

```go
package Divide
import "errors"

func divide(a int, b int) (num int, err error) {   // 定义两个返回值
    return a / b
    if b == 0{
        err := errors.New("被除数不能为零！")
        return
    }
    return a / b, nil
}
```

## 匿名函数

匿名函数由一个不带函数名的函数声明和函数体组成：

```go
func (a, b, c int) bool {
	return a * b < c
}
```

可以将匿名函数直接赋值给一个变量，也可以直接调用运行

```go
x := func (a, b, c, int) bool {
	return a * b < c
}

func (a, b, c int) bool {
	return a * b < c
}(1, 2, 3)           // 匿名函数直接调用
```

## 类型处理

### 类型转换

```go
x := int16(2345)
y := int32(x)
a := uint16(654000)
b := int16(a)

type StringsSlice []string    // 将 []string 声明为 StringSlice 类型
```



### 类型断言

在 Go 语言中 **接口** 是一个自定义类型。它声明了一个或者多个方法。任何实现了这些方法的对象（类型）都满足这个接口。接口是完全抽象的，不能实例化。`interface{}` 类型表示一个空接口，任何类型都满足空接口。也就是说 `interface{}` 类型的值可以用于表示任意 Go 语言类型的值。

**一般情况下只有我们希望表达式是某种特定类型的值时才使用类型断言**。Go 语言中可以使用以下语法：

- `resultOfType, boolean := expression.(Type)`：安全的类型断言。
- `resultOfType := expression.(Type)`：非安全的类型断言，失败时程序会产生异常。





