# GO 语言特性

## 并发编程

引入 goroutine，实现快速高效的并发编程；调用关键字 go，就可以让函数以**协程**为单位进行运行。goroutine 内部采用管道 channel 进行消息传递，从而实现共享内存。



## 错误处理

函数通过返回错误类型 (`error`) 或者 `bool` 类型表明函数的执行结果，调用检查返回的错误类型值是否是 `nil` 来判断调用结果。并引入了 defer 关键字用于标准的错误处理流程，并提供了内置函数 `panic`、`recover` 完成异常的抛出与捕捉。



## 垃圾回收

自动回收的功能，无需手动管理



## 多返回值

类似 Python 函数，一个函数返回多个值



## 匿名函数

```go
f := func(x, y) int {
    return x + y
}
```





# Hello World

创建 `hello.go `文件

```go
package main

import (
    "fmt"       # 导入 fmt 包，调用其中的 Println() 函数
)

func main() {
    fmt.Println("Hello, world!")
}

```

在终端运行：

```shell
>>> go run hello.go
```



# GO 语言基础

## 常量

使用关键字 const 声明，可以指定常量类型，但不是必需的。无类型常量，也叫字面常量：

```go
const limit = 512
const Pi float64 = 3.1415126
const x, y int = 1, 3              # 多常量同时赋值

const (                            # 同时定义多个常量
	Cyan = 0
    Black = 1
    White = 2
)
```

Go 语言预定义常量：true，false，iota

```go
const (
	a = iota    // a=0; const 关键字出现时，iota 重置为 0 
    b = iota    // b=1; iota 每出现一次，其值加 1
    c = iota    // c=2; 
)
const d = iota  // d=0; onst 关键字出现时，iota 重置为 0
```



## 变量

关键字 var 对变量声明，或 := 对变量直接初始化，编译器自动推导出改变量的类型

```go
var a int
var b string
var d [5] int      // 数组
var f * int
var v1 int = 5
var v2 = 5         // 编译器自动推导数据类型
v3 := 4            // 编译器自动推导数据类型
```

多重赋值

```go
i := 2
j := 30
i, j = j, i        // 无需中间值，直接交换两者的值
```



## 数据类型

### 整型

byte(uint8)

int

int8：[-128,127]

int16

int32

int64

rune

unit8：[0,255]

unit16

unit32

uint64

uintptr：容纳指针值的无符号整型



可以通过 unsafe.Sizeof 函数查看类型的子节长度

```go
package main       // 首先声明了目前的源文件属于 main 包

import (           // 导入了 fmt 和 unsafe 包
	"fmt"
	"unsafe"
)

func main() {      //  main() 函数
	a := 12
	fmt.Println("length of a :", unsafe.Sizeof(a))    // 打印变量 a 的字节长度
	var b int = 12
	fmt.Println("length of b(int) :", unsafe.Sizeof(b))
	var c int8 = 12
	fmt.Println("length of c(int8) :", unsafe.Sizeof(c))
	var d int64 = 12
	fmt.Println("length of d(int64) :", unsafe.Sizeof(d))
}
```



### 浮点型

float32，float64，complex32，complex64



### 布尔类型

true， false

布尔类型的变量，不能接受其它类型的赋值，不支持自动或强制的类型转换



### 字符串

Go语言中的字符串时 UTF-8 字符序列，使用双引号 `"`  或反引号 `` ` 创建。

双引号创建可解析的字符串字面量，一些符号可以被格式化为其它内容，如 `\n` 符号会被转义层换行符；反引号创建的字符串不会转义。

#### 字符串切片

字符串支持切片操作，但索引是通过内存的字节进行操作的，因此对于多字节组成的非 ASCII 字符，就需要特别注意；此时使用 range 操作符，按照字符进行切片

```go 
package main

import (
    "fmt"
)

func main() {
    t0 := "\u6B22\u8FCE\u6765\u5230" // t0内容：欢迎来到
    t1 := "\u5B9E\u9A8C\u697C"       // t1内容：实验楼
    t2 := t0 + t1
    for index, char := range t2 {
        fmt.Printf("%-2d    %U      '%c'    %X      %d\n",
            index, char, char, []byte(string(char)), len([]byte(string(char))))
    }
    fmt.Printf("length of t0: %d, t1: %d, t2: %d\n", len(t0), len(t1), len(t2))
    fmt.Printf("content of t2[0:2] is: %X\n", t2[0:2])
}
```

运行上述代码，输出如下：

```shell
$ go run string_t.go
0     U+6B22      '欢'    E6ACA2      3
3     U+8FCE      '迎'    E8BF8E      3
6     U+6765      '来'    E69DA5      3
9     U+5230      '到'    E588B0      3
12    U+5B9E      '实'    E5AE9E      3
15    U+9A8C      '验'    E9AA8C      3
18    U+697C      '楼'    E6A5BC      3
length of t0: 12, t1: 9, t2: 21
content of t2[0:2] is: E6AC
```



字符串切片操作

<table>
<thead>
<tr>
<th>语法</th>
<th>描述</th>
</tr>
</thead>
<tbody><tr>
<td>s += t</td>
<td>将字符串 t 追加到 s 末尾</td>
</tr>
<tr>
<td>s + t</td>
<td>将字符串 s 和 t 级联</td>
</tr>
<tr>
<td>s[n]</td>
<td>从字符串 s 中索引位置为 n 处的原始字节</td>
</tr>
<tr>
<td>s[n:m]</td>
<td>从位置 n 到位置 m-1 处取得的字符（字节）串</td>
</tr>
<tr>
<td>s[n:]</td>
<td>从位置 n 到位置 len(s)-1 处取得的字符（字节）串</td>
</tr>
<tr>
<td>s[:m]</td>
<td>从位置 0 到位置 m-1 处取得的字符（字节）串</td>
</tr>
<tr>
<td>len(s)</td>
<td>字符串 s 中的字节数</td>
</tr>
<tr>
<td>len([]rune(s))</td>
<td>字符串 s 中字符的个数，可以使用更快的方法 utf8.RuneCountInString()</td>
</tr>
<tr>
<td>[ ]rune(s)</td>
<td>将字符串 s 转换为一个 unicode 值组成的串</td>
</tr>
<tr>
<td>string(chars)</td>
<td>chars 类型是 []rune 或者 []int32, 将之转换为字符串</td>
</tr>
<tr>
<td>[ ]byte(s)</td>
<td>无副本的将字符串 s 转换为一个原始的字节的切片数组，不保证转换的字节是合法的 UTF-8 编码字节</td>
</tr>
</tbody></table>

#### 格式化字符串

目前使用了两种打印函数：fmt.Println，fmt.Printf。后者可以提供一些格式化指令，对输出字符串进行格式化。常见的格式化指令：

<table>
<thead>
<tr>
<th>格式化指令</th>
<th>含义</th>
</tr>
</thead>
<tbody><tr>
<td>%%</td>
<td>% 字面量</td>
</tr>
<tr>
<td>%b</td>
<td>一个二进制整数，将一个整数格式化为二进制的表达方式</td>
</tr>
<tr>
<td>%c</td>
<td>一个 Unicode 的字符</td>
</tr>
<tr>
<td>%d</td>
<td>十进制数值</td>
</tr>
<tr>
<td>%o</td>
<td>八进制数值</td>
</tr>
<tr>
<td>%x</td>
<td>小写的十六进制数值</td>
</tr>
<tr>
<td>%X</td>
<td>大写的十六进制数值</td>
</tr>
<tr>
<td>%U</td>
<td>一个 Unicode 表示法表示的整形码值，默认是 4 个数字字符</td>
</tr>
<tr>
<td>%s</td>
<td>输出以原生的 UTF-8 字节表示的字符，如果 console 不支持 UTF-8 编码，则会输出乱码</td>
</tr>
<tr>
<td>%t</td>
<td>以 true 或者 false 的方式输出布尔值</td>
</tr>
<tr>
<td>%v</td>
<td>使用默认格式输出值，或者使用类型的 String() 方法输出的自定义值，如果该方法存在的话</td>
</tr>
<tr>
<td>%T</td>
<td>输出值的类型</td>
</tr>
</tbody></table>

除了上述格式化指令外，还可以使用一些格式化修饰符，改变输出结果，如左对齐等等

- 空格，输出的数字为负，则在其前面加上一个减号 `-`  。如果输出的是整数，则在前面加一个空格 `fmt.Printf("% X", "实")` 
- #
  - `%#o` 输出以 `0` 开始的八进制数据。
  - `%#x` 输出以 `0x` 开始的十六进制数据。
- `+` 让格式化指令在数值前面输出 `+` 号或者 `-` 号，为字符串输出 ASCII 字符（非 ASCII 字符会被转义），为结构体输出其字段名。
- `-` 让格式化指令将值向左对齐（默认值为向右对齐）。
- `0` 让格式指令以数字 0 而非空白进行填充。



## 数组

Go 语言的数组是一个**定长(一经创建，长度固定不可修改)**的序列，其中元素类型相同。创建数组：

```pseudocode
[length]Type
[N]Type{val1,val2...valN}
[...]Type{val1,val2...valN}  // ... 操作符，Go 语言会自动计算数组的长度
```

数组的长度 `len()` 函数获得，数组的容量 `cap()` 函数，两者返回的值相同

使用操作符 `[]` 来索引，也可以使用 `range` 进行索引访问。

### 数组切片

数组是定长的，按值传递；而切片是引用类型，可以调整长度，传递切片的成本非常小

常见切片的语法：

```pseudocode
make([]Type, length, capacity)
make([]Type, length)
[]Type{}
[]Type{val1,val2,...,valN}
```

