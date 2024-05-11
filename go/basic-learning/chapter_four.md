### chapter_four

#### 并发编程：多个进程并发

#### 协程

可以轻松创建上百万个而不会导致系统资源衰竭，而线程和进程通常最多也不能超过1万个

#### goroutine

轻量级线程实现，由Go运行时（runtime）管理

#### 并发通信

共享数据和消息

共享数据是指多个并发单元分别保持对同一个数据的引用，实现对该数据的共享。在实际工程应用中最常见的是共享内存。

消息机制而非共享内存作为通信方式，每个并发单元的输入和输出只有一种，那就是消息。

消息通信机制被称为channel

#### channel

goroutine间的通信方式。

一个channel只能传递一种类型的值，这个类型需要在声明channel时指定

##### 基本语法

````go
//声明形式
var chanName chan ElementType 
//举个例子
var ch chan int 
var m map[string] chan bool 
//定义channel，直接用内置函数mack()
ch := make(chan int) 
//写入，向channel写入数据通常会导致程序阻塞
ch <- value 
//读出
value := <-ch 
````

如果需要跨进程通信，我们建议用分布式系统的方法来解决，比如使用Socket或者HTTP等通信协议。

##### select

用于处理异步IO问题。

用法与switch语言非常类似。

由select开始一个新的选择块，每个选择条件由case语句来描述。最大限制就是每个case语句里必须是一个IO操作。

```go
select{
    case <-chan1: //如果chan1成功读到数据，则进行该case处理语句
    case chan2 <- 1://如果成功向chan2写入数据，则进行该case处理语句
    default:  //如果上面都没有成功，则进入default处理流程
}
```

##### 缓冲机制

持续传输大量数据的场景

创建一个带缓冲的channel

```go
c := make(chan int, 1024) //大小为1024的int类型channel,写入方可以一直往channel里写入，在缓冲区被填完之前都不会阻塞
```

调用make()时将缓冲区大小作为第二个参数传入即可

##### 超时机制

在并发编程的通信过程中，最需要处理的就是超时问题，即向channel写数据时发现channel已满，或者从channel试图读取数据时发现channel为空。

利用select机制能很方便地解决超时问题

select的特点是只要其中一个case已经完成，程序就会继续往下执行，而不会考虑其他case的情况

```go
// 首先，我们实现并执行一个匿名的超时等待函数
timeout := make(chan bool, 1) 
go func() { 
 time.Sleep(1e9) // 等待1秒钟
 timeout <- true
}() 
// 然后我们把timeout这个channel利用起来
select { 
 case <-ch: 
 // 从ch中读取到数据
 case <-timeout: 
 // 一直没有从ch中读取到数据，但从timeout中读取到了数据
}
```

##### channel的传递

以使用这个特性来实现*nix上非常常见的管道（pipe）特性

在处理数据时，我们可以采用管道设计，这样可以比较容易以插件的方式增加数据的处理流程

```go
//假设在管道中传递的数据只是一个整型数，在实际的应用场景中这通常会是一个数据块
type PipeData struct { 
 value int
 handler func(int) int
 next chan int
} 
//只要定义一系列PipeData的数据结构并一起传递给这个函数，就可以达到流式处理数据的目的
func handle(queue chan *PipeData) { 
 for data := range queue { 
 data.next <- data.handler(data.value) 
 } 
} 
```

##### 单向channel 

用于发送或者接收数据,支持读写

##### 关闭channel 

```go
close(ch) 
//怎样确定是否关闭，在读取的时候使用多重返回值的方式
x, ok := <-ch 
```

#### 多核并行化

利用现代服务器普遍具备的多核特性来尽量将任务并行化，从而达到降低总计算时间的目的。需要了解CPU核心的数量，并针对性地分解计算任务到多个goroutine中去并行运行

goroutine简化了我们写并行代码的过程，但实际上整体运行效率并不真正高于单线程程序

可以先通过设置环境变量GOMAXPROCS的值来控制使用多少个CPU核心

* 直接设置环境变量GOMAXPROCS的值
* 在代码中启动goroutine之前先调用以下这个语句以设置使用16个CPU核心

```go
runtime.GOMAXPROCS(16)
```

#### 出让时间片

使用runtime包中的Gosched()函数实现

#### 同步

##### 同步锁

sync包提供了两种锁类型：sync.Mutex和sync.RWMutex

Mutex是最简单的一种锁类型,当一个goroutine获得了Mutex后，其他goroutine就只能乖乖等到这个goroutine释放该Mutex。

RWMutex是经典的单写多读模型，在读锁占用的情况下，会阻止写，但不阻止读，也就是多个goroutine可同时获取读锁（调用RLock()方法）；而写锁（调用Lock()方法）会阻止任何其他goroutine（无论读和写）进来，整个锁相当于由该goroutine独占
