#### go环境变量与工作目录

bin：存放编译后生成的可执行文件

pkg：存放编译后生成的包文件

src：存放项目源码

#### go常用命令

go run:运行程序

go build:测试编译

go fmt:格式化源码

go install:编译包文件并编译整个程序

go test:运行测试文件

go doc:查看文档

#### go语言的"Hello world!"

```go
   package main
   import(
       "fmt"
   )
   func main(){
      fmt.println("Hello world!你好，世界！")
   }
```

gosublime调出内置命令行快捷键 Ctrl+B

#### Go内置关键字(25个均为小写)

|    break     |     default     |    func    |  interface  |   select   |
| :----------: | :-------------: | :--------: | :---------: | :--------: |
|   **case**   |    **defer**    |   **go**   |   **map**   | **struct** |
|   **chan**   |    **else**     |  **goto**  | **package** | **switch** |
|  **const**   | **fallthrough** |   **if**   |  **range**  |  **type**  |
| **continue** |     **for**     | **import** | **return**  |  **var**   |

#### go程序一般结构

```go
//当前程序的包名
package main
//导入其他包
import "fmt"
//常量的定义
const PI = 3.14
//全局变量的声明与赋值
var name  = "gopher"
//一般类型的声明
type newType int
//结构的声明
type gopher struct{}
//接口的声明
type goland interface{}
//由 main 函数作为程序入口点启动
func main{
    Println("Hello world!你好，世界！")
}
```

#### Go导入package的格式

```go
import(
   "fmt"
   "io"
   "os"
)
```

* 若导入包**未调用**其中的**函数**或**类型**将会出现编译错误

#### 可见性规则

* 通过**大小写**来决定该*常量*、*变量*、*类型*、接口、结构或函数是否可以 被外部包所调用
* 根据约定：函数**首字母大写**即可以被**外部**包调用，**小写**即为**内部**包调用

#### Go基本类型

* 布尔类型：bool，长度1字节，取值范围：true和false
* 整型：int/uint，32/64
* 8位整型：int8/uint8,长度1字节，取值范围：-127~128/0到255
* 字节型：byte（uint8别名）
* rune：int32别名，长度4字节
* 浮点型：float32/float64,长度4/8字节，小数位精确到7/15位
* 复数：complex64/complex128,长度8/16字节
* 足够保存指针的32位或64位整型数型：uintptr
* 其他值类型：array,struct,string
* 引用类型：slice,map,chan
* 接口类型：interface
* 函数类型：func

#### 类型零值

不等于空值，通常情况值类型默认为0，bool为false，string为空字符串

```go
package mian
import (
    "fmt"
)
func main( ) {
    var a int
    fmt.Println(a)
}
//输出为0
package mian
import (
    "fmt"
)
func main( ) {
    var a bool
    fmt.Println(a)
}
//输出为false
```

#### 单个变量的声明与赋值

变量的声明格式：var<变量名称><变量类型>

变量的赋值格式：<变量名称> = <表达式>

声明同时赋值：var<变量名称> [变量类型] = <表达式>

```go
var b int = 1
//或
b:=1
```

#### 多个变量的声明与赋值

全局变量的声明可使用var()的方式进行简写

```go
var a,b,c,d int = 0,5,2,6{
    fmt.Println(a)
    fmt.Println(b)
    fmt.Println(c)
    fmt.Println(d)
}
```

全局变量的声明不可省略var，但可以使用并行方式

所有变量都可以使用类型推断

局部变量不可使用var()方式简写，只能使用并行方式

#### 变量的类型转换

所有类型转换必须显式声明

转换只能发生在两种相互兼容的类型之间

```go
// 格式：<ValueA>[:] = <TypeOfValueA>(<ValueB>)
func main(){
    var a float32 = 100.1
    fmt.Println(a)
    b := int(a)
    fmt.Println(b)
}
//输出a = 100.1，b = 100
```

#### 常量的定义

等号右侧必须是常量或常量表达式

常量表达式中的函数必须是内置函数，即常量的值在编译时就已经确定

```go
package main
import(
    "fmt"
)
const (
    a = 526
    b
    c
)
func main(){
    fmt.Println(a)
    fmt.Println(b)
    fmt.Println(c)
}
//输出结果都为526，b默认了a的表达式，c默认了b


const (
    a = "526" //常量
    b = len(a) //内置函数
    c
)
func main(){
    fmt.Println(a)
    fmt.Println(b)
    fmt.Println(c)
}
//输出结果为a = 526 ，b = 3（a的长度），c与b一样
```

#### 枚举

iota是常量计数器，从0开始，组中每定义1个常量自动递增1，每遇到const，iota重置为0

```go
package main
import (
    "fmt"
)
const (
    a = "526"
    b = iota
    c = "B"
    d = 
)
const (
    e = iota
)
func main(){
    fmt.Println(a)
    fmt.Println(b)
    fmt.Println(c)
    fmt.Println(d)
    fmt.Println(e)
}
//a = 526 ,b = 1 ,c =527 ,d = 3,e = 0
```

#### 运算符

优先级（从高到低）

* ^   !(取反)
* *,    /     %    <<   >>    &    &^
* +,   -    |   ^
* ==     !=    <    <=   >=    >
* <-(专用于channel)
* &&
* ||

```go
fmt.Println(1 << 10)
//输出1024，B到KB
fmt.Println(1 << 10 << 10)
//输出1048576，KB到MB

/*
6: 0110
11:1011
---------
&  0010 = 2
|  1111 = 15
^  1101 = 13
&^ 0100 = 4
*/

func main(){
    a := 0
    if a > 0 && (10/a) < 1{
         fmt.Println("OK")
    }  
} 
//&&左条件通过再执行右边
```

栗子 ：结合常量的iota与<<运算符实现计算机储存单位的枚举

```go
package main
import (
    "fmt"
)
const (
    B float64 = 1 << (iota * 10)
    KB
    MB
    GB
)
func main(){
    fmt.Println(KB)
}
//KB = 1024
```

#### 指针：运算符采用" . "，操作符"&"取变量地址，使用" * "通过指针间接访问目标对象，默认值为nil

```go
func main(){
    a := 1
    var p *int = &a
    fmt.Println(*p)    
}
//p指针指向地址的值，也就是a的值，1
```

#### 递增递减语句，Go中，"++"和"--"是作为语句，不是表达式

```go
func main(){
    a := 1
    a++
    var p *int = &a
    fmt.Println(*p)    
}
//p指针指向地址的值，也就是a的值，2
```

#### 判断语句if

if后面没括号，





