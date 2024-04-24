### chapter two_2

### 流程控制   函数  错误示范

#### 指针

运算符采用" . "，操作符"&"取变量地址，使用" * "通过指针间接访问目标对象，默认值为nil

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

#### 判断（条件）语句if

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



