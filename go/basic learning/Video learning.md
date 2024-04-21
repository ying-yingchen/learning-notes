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

if后面没括号，支持初始化表达式（并行方式）

"{"必须在条件语句或else同一行

#### 循环语句for

```go
//第一种
func main(){
    a := 1
    for{
        a++
        if a > 3{
            break
        }
        fmt.Println(a)
    }
      fmt.Println("over")
}
//2,3,over

//第二种
func main(){
    a := 1
    for a  <= 3{
        a++
        fmt.Println(a)
    }
      fmt.Println("over")
}
//2,3,4,over

//第三种
func main(){
    a := 1
    for i= 0；i< 3;i++{
        a++
        fmt.Println(a)
    }
      fmt.Println("over")
}
//2,3,4,over
```

#### 选择语句switch

可以使用任何类型或表达式作为条件语句

不需写break，条件符合自动终止

若要继续执行下一个case，可使用fallthrough语句

```go
func main(){
    a := 1 
    switch a{
    case a >= 0:
        fmt.Println("a=0")
        fallthrough
        case a >= 1:
        fmt.Println("a=1")
        default:
        fmt.Println("None")
    }
}
//a=0,a=1
```

#### 跳转语句goto,break,conti

都可配合标签使用。标签名要区分大小写

break和continue配合标签可用于多层循环的跳出

goto是调整执行位置

```go
func main(){
    LABEL1:
    for{
        for i := 0;i < 10;i++{
          if i > 3 {
            break LABEL1   
        }
      }
    }
    fmt.Println("OK")
}
//OK,break后没加LABEL1将会无限循环，break LABEL1代表结束LABEL1的循环 ,LANEL1与最外围的for是一级的

func main(){
    LABEL1:
    for i := 0;i < 10;i++{
        for {
            continue LABEL1 
            fmt.Println(i)
        }
    }
     fmt.Println("OK")
}
//OK,内部的for是无线循环的，所以i不会输出，continue跳到外围的与LABEL1同级的for（有限的）进行执行才能输出"OK"
```

#### 数组Array

定义数组的格式：var<varName>[n]<type>,n>=0

```go
func main(){
    a :=[2]int{1}
    fmt.Println(a)
}
//输出[1 0]

func main(){
    a :=[3]int{2：1}
    fmt.Println(a)
}
//输出[0 0 1]

func main(){
    a :=[...]int{0:5,1:2,2:6}
    fmt.Println(a)
}
//输出[5 2 6]
```

数组之间可以用==或！=进行比较

```go
func main(){
    a := [2][3]int{
        {5,2,6},
        {1,7,2}
        fmt.Println(a)
    }
}
//输出[[5 2 6][1 7 2]]
```

```go
func main(){
    a := [...]int{5,2,6,1,7,2}
    fmt.Println(a)
    
    num := len(a)
    for i := 0;i < num;i++{
        for j := i+1;j <num:J++{
            if a[i]<[j]{
                tem := a[i]
                a[i] = a[j]
                a[j] = temp
            }
        }
    }
    fmt.Println(a)
}
//冒号排序
```

#### 切片slice

是指向底层的数组，为引用类型，可以直接创建或从底层数组获取生成

使用len( )获取元素个数，cap( )获取容量

一般使用make( )创建

若多个slice指向相同底层数组，其中一个的值改变会影响全部

```go
make([]T,len,cap)
```

其中cap可以省略，则和len值相同

```go
func main(){
    a := [10]int{1,2,3,4,5,6,7,8,9}
    fmt.Println(a)
    s1 :=a[5:10]// a[5 6 7 8 9],不包含10
    fmt.Println(s1)
}
// [1 2 3 4 5 6 7 8 9 0];[6 7 8 9 0]
//s1 :=a[5:len(a)]代表a（一定有5或大于5）中取5个
//s1 :=a[5:]第5到尾部；s1 :=a[:5]前5个元素

func main(){
    s1 := make([]int,3,10)
    fmt.Println(len(s1),cap(s1))
    fmt.Println(s1)
}
//3 ;10 ;[0 0 0]
```

#### Reslice

索引是以被slice为准

索引不可越界

```{
func main( ){
     a := []byte{'a','b','c','d','e','f','g','h','i'}
     sa := a[2:5]
     sb := sa[1:3]
     fmt.Println(string(sb))
}
//sa输出cde，sb输出de
```

#### Append

在slice尾部追加元素或另一个slice

拼接后的容量大于原slice所允许的容量，此时会重新分配数据并拷贝原始数据

```go
func main( ){
    s1 := make([]int,3,6)
    fmt.Println("%p",s1)
    s1=append(s1,5,2,6)
    fmt.Println("%v","%p",s1,s1)
}
//输出第一个地址，[0 0 0 5 2 6]输出相同的地址，因为加的slice没有超过原slice，所以输出相同的地址

func main( ){
    s1 := make([]int,3,6)
    fmt.Println("%p",s1)
    s1=append(s1,5,2,6)
    fmt.Println("%v","%p",s1,s1)
    s1=append(s1,1,7,2)
    fmt.Println("%v","%p",s1,s1)
}
//输出第一个地址，[0 0 0 5 2 6]输出与第一个相同的地址，[0 0 0 5 2 6 1 7 2]输出一个新的地址，即拼接后容量大于原slice，所以出现新的地址
```

#### copy

```go
func main(){
    s1 := []int{5,2,6,1,7,2}
    s2 := []int{0,9,0}
    copy(s2,s1)
    fmt.Println(s1)
}
//输出[5,2,6],将s1的前三个元素copy到s2，因为s2比较短，只会拷贝s2相同数量的元素，反之，如果copy(s1,s2)将s2copy到s1，则会输出[0 9 0 1 7 2]。
//也可以之拷贝其中一部分
func main(){
    s1 := []int{5,2,6,1,7,2}
    s2 := []int{0,9,0,0,1,24,1,5,8}
    copy(s2[2:4],s1[1:3])
    fmt.Println(s2)
}
//输出[0 9 2 6 1 24 1 5 8]
```

#### map:key-value

使用make()创建

```go
//第一种
func main(){
    var m map[int]string
    m = map[int]string{}
    fmt.Println(m)
}
//第二种
func main(){
    var m map[int]string
    m = make(map[int]string{})
    fmt.Println(m)
}
//第三种
func main(){
    var m map[int]string = make(map[int]string{})
    fmt.Println(m)
}
//第四种
func main(){
    m := make(map[int]string{})
    fmt.Println(m)
}
//均输出map[]
```

```go
func main(){
    m := make(map[int]string{})
    m[1] = "OK"
    fmt.Println(m)
}
//输出map[1:OK]
func main(){
    m := make(map[int]string{})
    m[1] = "OK"//若不想要这个map，可加delete(m,1)到下一行，即a输出为空
    a := m[1]
    fmt.Println(a)
}
//取出map的值，即输出"OK"

//复杂map，多层map，要记得初始化
func main(){
    var m map[int]map[int]string
    m = make(map[int]map[int]string)
    a,ok := m[2][1]
    if !ok{
        m[2] = make(map[int]string)//第二层map初始化
    }
    m[2][1] = "GOOD"
    a,ok = m[2][1]
    fmt.Println(a,ok)
}
//输出GOOD true
```

 ```go
 //map中k(key)的间接排序
 package main
 import(
     "fmt"
     "sort"
 )
 func main(){
     m := map[int]string{1:"a",2:"b",3:"c",4:"d",5:"e"}
     s := make([]int,len(m))
  //给map迭代
     i := 0   
     for k,_ := range m{
         s[i] = k
         i++     
     }
     sort.Ints(s) //没有导入这个包的时候，每次输出结果为[2 1 4 3 5]或[3 4 2 1 5],即结果为无序的，当导入sort这个包的时候，每次打印结果都是有序的[1 2 3 4 5]
     fmt.Println(s)
 }
 //将类型为map[int]string的键和值进行交换，变成类型map[string]int
 func main(){
     m1 := map[int]string{5:"a",2"b",6:"c"}
     fmt.Println(m1)
     m2 := make(map[string]int)
     for k,v :=range m1{
         m2[v] = k
     }
     fmt.Println(m2)
 }
 //map[5:a 2:b 6:c] map[a:5 b:2 c:6]
 ```

#### 函数function

go函数不支持嵌套、重载和默认参数

但支持：无需声明原型、不定长度变参多返回值、命名返回值参数、匿名函数、闭包

```go 
func main(){
    a := func(){
        fmt.Println("Func A")
    }      //匿名函数
    a()    //调用函数
}
func A(){
    fmt.Println("Func A")
}
//输出Func A

//闭包操作:返回一个匿名函数
func main(){
    f := closure(10)
    fmt.Println(f(1)) //输入1
    fmt.Println(f(2)) //输入2
}
func closure(x int)func(int) int {
    fmt.Println("%p\n",&x) //输出第一次的地址
    return func(y int) int {
        fmt.Println("%p\n",&x)
        return x + y
    }
}
//输出地址 地址 11 地址 12（三次地址一样，输入1和2会出现地址），三次调用都是同一个x
```

#### defer

类似析构函数，在函数执行结束后按照调用顺序的**相反顺序**逐个执行

即使函数发生严重错误也会执行

常用于资源清理、文件关闭、解锁以及记录时间等操作

通过与匿名函数配合可以在return后修改函数计算结果

```go
func main(){
    fmt.Println("a")
    defer fmt.Println("b")
    defer fmt.Println("c")
}//输出a c b,后定义先调用

func main(){
    for i := 0;i < 3;i++{
        defer func(){
         fmt.Println(i)   
        }()     //括号代表defer调用函数
    }
}//输出3 3 3
```

go用panic/recover模式来处理错误

panic可以在任何地方引发，recover只在defer调用的函数有效

```go
func main(){
    A()
    B()
    C()
}
func A(){
    fmt.Println("Func A")
}
func B(){
    defer func(){
        if err:=recover();err!=nil{
            fmt.Println("Recover in B")
        }
    }()
    panic("panic in B")
   
}
func C(){
    fmt.Println("Func C")
}
//输出Func A Recover in B Func C
//没有用defer的话，panic会终止程序，就不会输出Func C
```



