## chapter one

### 初识

#### Go语言主要特性

1.自动垃圾回收：所有内存分配动作及内存使用都会被记录，并对所有已分配的内存进行跟踪检测，当有些内存没有被使用时就进行阶段性地回收（CPU相对空闲的时候）

2.更丰富的内置类型：简单的（比如整型和浮点型）、C#和Java中数组和字符串、map字典类型、slice数组切片(切片属性：开始位置、长度、最大长度|切片：引用类型)

3.函数多返回值

4.错误处理（可大量减少代码）：defer,panic,recover

5.匿名函数和闭包：所有函数也为值类型，可作参数传递

6.类型和接口：可以不用声明关系直接转换

7.并发编程：goroutine概念（普通函数或方法，通过关键字go启动新的goroutine），通过channel可方便进行跨goroutine通信

8.反射

9.语言交互性：Cgo

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

代码解读

1.package声明：Go代码所属的包（包：最基本的分发单位）

2.生成可执行程序，必须建立main，并在该包中包含main( )的函数

3.命令行传入的参数在os.Args变量中保存（os包以跨平台的方式提供与操作系统交互的函数和变量）

4.flag包用于支持命令行开关

5.包声明是一系列import语句，用于导入该程序所依赖的包。例：Println( )所属的包是fmt包

6.强制左花括号{，以及一些函数名的大小写规则

7.Go函数以关键字func开头

```go
func 函数名(参数列表)(返回值列表){
    //函数体
}
```

8.并不是所有返回值都必须赋值，没有被明确赋值的返回值会被设置默认值（例：result会被设为0.0，err会被设为nil）

9.注释

```go
//注释
/*注释*/
```

问题追踪和调试：打印日志（log包）、GDB逐步调试

#### 可见性规则

通过**大小写**来决定该*常量*、*变量*、*类型*、接口、结构或函数是否可以 被外部包所调用

根据约定：函数**首字母大写**即可以被**外部**包调用，**小写**即为**内部**包调用

