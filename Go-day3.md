

# 错误处理

Go 语言引入了一个错误处理的标准模式，即 `error` 接口，定义如下：

```go
type error interface {
	Error() string
}
```

对于大多数函数，如果要返回错误，可以将 `error` 作为多返回值的最后一个：

```go
func fool(param int)(ret int, err, error){
	....
}
```

调用时的代码：

```go
n, err := foo(0)
if err != nil {
	// 错误处理
} else {
	// 使用返回值
}
```

可以自定义错误类型：

```go
package main

import "fmt"
import "errors"

//自定义的出错结构
type myError struct {
    arg  int
    errMsg string
}
//实现Error接口
func (e *myError) Error() string {
    return fmt.Sprintf("%d - %s", e.arg, e.errMsg)
}

//两种出错
func error_test(arg int) (int, error) {
    if arg < 0  {
         return -1, errors.New("Bad Arguments - negtive!")
     }else if arg >256 {
        return -1, &myError{arg, "Bad Arguments - too large!"}
    }
    return arg*arg, nil
}

//相关的测试
func main() {
    for _, i := range []int{-1, 4, 1000} {
        if r, e := error_test(i); e != nil {
            fmt.Println("failed:", e)
        } else {
            fmt.Println("success:", r)
        }
    }
}
```

在 Go 函数中添加多个 `defer` 语句，当函数执行到最后时，这些 defer 语句会按照逆序执行（即最后一个 `defer` 语句将最先执行），最后该函数返回。特别是当你在进行一些打开资源的操作时，遇到错误需要提前返回，在返回前你需要关闭相应的资源，不然很容易造成资源泄露等问题。

```go
func CopyFile(dst, src string) (w int64, err error) {
	srcFile, err := os.Open(src)
	if err != nil {
		return
	}
	
	defer srcFile.Close()
	
	dstFile, err := os.Create(dst)
	if err != nil {
		return
	}
	defer dstFile.close()
	
	return io.Copy(dstFile, srcFile)
}
```

`panic()` 函数用于抛出异常，`recover()` 函数用于捕获异常

```go
func panic(interface{})
func recover() interface{}
```

