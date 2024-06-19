### chapter_five

#### 网络编程

net包，支持基于IP层、TCP/UDP层及HTTP、FTP、SMTP的网络操作

#### Socket

无论使用什么协议建立什么形式的连接，只需要调用net.Dial()即可。

##### Dial()函数

Dial()函数的原型：func Dial(net, addr string) (Conn, error) 

net参数是网络协议的名字，addr参数是IP地址或域名

而端口号以“:”的形式跟随在地址或域名的后面，端口号可选

连接成功，返回连接对象，否则返回error

几种常见协议的调用方式

```go
//TCP链接：
conn, err := net.Dial("tcp", "192.168.0.10:2100") 
//UDP链接：
conn, err := net.Dial("udp", "192.168.0.12:975")
//ICMP链接（使用协议名称）：
conn, err := net.Dial("ip4:icmp", "www.baidu.com") 
//ICMP链接（使用协议编号）：
conn, err := net.Dial("ip4:1", "10.0.0.3") 
```

目前，Dial()函数支持如下几种网络协议："tcp"、"tcp4"（仅限IPv4）、"tcp6"（仅IPv6）、"udp"、"udp4"（仅限IPv4）、"udp6"（仅限IPv6）、"ip"、"ip4"（仅限IPv4）和"ip6" （仅限IPv6）。

##### ICMP示例程序

icmptest.go

```go
package main 
import ( 
 "net" 
 "os" 
 "bytes" 
 "fmt" 
) 
func main() { 
 if len(os.Args) != 2 { 
 fmt.Println("Usage: ", os.Args[0], "host") 
 os.Exit(1) 
 } 
 service := os.Args[1] 
 conn, err := net.Dial("ip4:icmp", service) 
 checkError(err) 
 var msg [512]byte
 msg[0] = 8 // echo 
 msg[1] = 0 // code 0 
 msg[2] = 0 // checksum 
 msg[3] = 0 // checksum 
 msg[4] = 0 // identifier[0] 
 msg[5] = 13 //identifier[1] 
 msg[6] = 0 // sequence[0] 
 msg[7] = 37 // sequence[1] 
len := 8 
    check := checkSum(msg[0:len]) 
 msg[2] = byte(check >> 8) 
 msg[3] = byte(check & 255) 
 _, err = conn.Write(msg[0:len]) 
 checkError(err) 
 _, err = conn.Read(msg[0:]) 
 checkError(err) 
 fmt.Println("Got response") 
 if msg[5] == 13 { 
 fmt.Println("Identifier matches") 
 } 
if msg[7] == 37 { 
 fmt.Println("Sequence matches") 
 } 
 os.Exit(0) 
} 
func checkSum(msg []byte) uint16 { 
 sum := 0 
 // 先假设为偶数
for n := 1; n <len(msg)-1; n += 2 { 
 sum += int(msg[n])*256 + int(msg[n+1]) 
 } 
 sum = (sum >> 16) + (sum & 0xffff) 
 sum += (sum >> 16) 
var answer uint16 = uint16(^sum) 
return answer 
} 
func checkError(err error) { 
if err != nil { 
 fmt.Fprintf(os.Stderr, "Fatal error: %s", err.Error()) 
 os.Exit(1) 
 } 
} 
func readFully(conn net.Conn) ([]byte, error) { 
 defer conn.Close() 
 result := bytes.NewBuffer(nil) 
 var buf [512]byte
for { 
 n, err := conn.Read(buf[0:]) 
 result.Write(buf[0:n]) 
 if err != nil { 
 if err == io.EOF { 
 break 
 } 
return nil, err 
 } 
 } 
return result.Bytes(), nil
} 
```

##### TCP示例程序

simplehttp.go 

```go
package main 
import ( 
 "net" 
 "os" 
 "bytes" 
 "fmt" 
) 
func main() { 
if len(os.Args) != 2 { 
 fmt.Fprintf(os.Stderr, "Usage: %s host:port", os.Args[0]) 
 os.Exit(1) 
 } 
 service := os.Args[1] 
 
 conn, err := net.Dial("tcp", service) 
 checkError(err) 
 _, err = conn.Write([]byte("HEAD / HTTP/1.0\r\n\r\n")) 
 checkError(err) 
 result, err := readFully(conn) 
 checkError(err) 
 fmt.Println(string(result)) 
 os.Exit(0) 
} 
func checkError(err error) { 
if err != nil { 
 fmt.Fprintf(os.Stderr, "Fatal error: %s", err.Error()) 
    os.Exit(1) 
 } 
} 
func readFully(conn net.Conn) ([]byte, error) { 
 defer conn.Close() 
 result := bytes.NewBuffer(nil) 
var buf [512]byte
for { 
 n, err := conn.Read(buf[0:]) 
 result.Write(buf[0:n]) 
 if err != nil { 
 if err == io.EOF { 
 break 
 } 
 return nil, err 
 } 
 } 
 return result.Bytes(), nil
} 
```

#### HTTP编程：GET和POST方式请求数据

##### 基本方法

1. http.Get()等价于http.DefaultClient.Get()

2. http.Post() 

   请求的目标 URL 
   将要 POST 数据的资源类型（MIMEType）
   数据的比特流（[]byte形式）

#####  高级封装

可自定义的http.Client

```go
type Client struct { 
 // Transport用于确定HTTP请求的创建机制。
 // 如果为空，将会使用DefaultTransport 
 Transport RoundTripper 
 // CheckRedirect定义重定向策略。
 // 如果CheckRedirect不为空，客户端将在跟踪HTTP重定向前调用该函数。
 // 两个参数req和via分别为即将发起的请求和已经发起的所有请求，最早的
 // 已发起请求在最前面。
 // 如果CheckRedirect返回错误，客户端将直接返回错误，不会再发起该请求。
 // 如果CheckRedirect为空，Client将采用一种确认策略，将在10个连续
 // 请求后终止
 CheckRedirect func(req *Request, via []*Request) error 
 // 如果Jar为空，Cookie将不会在请求中发送，并会
 // 在响应中被忽略
 Jar CookieJar 
} 
```

http.Client类型包含了3个公开数据成员：

Transport RoundTripper 
CheckRedirect func(req *Request, via []*Request) error 
Jar CookieJar 

其中Transport类型必须实现http.RoundTripper接口。

CheckRedirect函数指定处理重定向的策略。

Jar可用于在 HTTP Client 中设定 Cookie。

#### HTTP服务端

如何处理HTTP请求和HTTPS请求

1. 处理HTTP请求

   使用 net/http 包提供的 http.ListenAndServe() 方法，可以在指定的地址进行监听，开启一个HTTP，服务端该方法的原型如下：func ListenAndServe(addr string, handler Handler) error 

2. 处理HTTPS请求

   net/http 包还提供 http.ListenAndServeTLS() 方法，用于处理 HTTPS 连接请求：
   func ListenAndServeTLS(addr string, certFile string, keyFile string, handler Handler) 
    error 

#### RPC 编程

是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络细节的应用程序通信协议。

RPC 采用客户端—服务器（Client/Server）的工作模式

#### Go语言中的RPC支持与处理

一个对象中只有满足如下这些条件的方法，才能被 RPC 服务端设置为可供远程访问：

1. 必须是在对象外部可公开调用的方法（首字母大写）；
2. 必须有两个参数，且参数的类型都必须是包外部可以访问的类型或者是Go内建支持的类
   型；
3. 第二个参数必须是一个指针；
4. 方法必须返回一个error类型的值。

以上4个条件可以简单地用如下一行代码:

```go
func (t *T) MethodName(argType T1, replyType *T2) error 
```

 RPC 服务端和客户端交互的示例程序

```go
package server 
type Args struct { 
 A, B int
} 
type Quotient struct { 
 Quo, Rem int
} 
type Arith int
func (t *Arith) Multiply(args *Args, reply *int) error { 
 *reply = args.A * args.B 
 return nil
} 
func (t *Arith) Divide(args *Args, quo *Quotient) error { 
 if args.B == 0 { 
 return errors.New("divide by zero") 
 } 
 quo.Quo = args.A / args.B 
 quo.Rem = args.A % args.B 
 return nil
} 
```

##### 设计优雅的RPC接口

RPC 提供的编码解码器接口如下：

```go
type ClientCodec interface { 
 WriteRequest(*Request, interface{}) error  
ReadResponseHeader(*Response) error 
 ReadResponseBody(interface{}) error 
 Close() error 
} 
type ServerCodec interface { 
 ReadRequestHeader(*Request) error 
 ReadRequestBody(interface{}) error 
 WriteResponse(*Response, interface{}) error 
 Close() error 
} 
```

### JSON 处理

JSON （JavaScript Object Notation）是一种比XML更轻量级的数据交换格式，在易于人们阅读和编写的同时，也易于程序解析和生成。

#### 编码为JSON格式

使用json.Marshal()函数可以对一组数据进行JSON格式的编码

声明如下：

```go
func Marshal(v interface{}) ([]byte, error) 
```

Go语言的大多数数据类型都可以转化为有效的JSON文本，但channel、complex和函数这几种类型除外。

在Go中，JSON转化前后的数据类型映射如下

布尔值转化为JSON后还是布尔类型。
 浮点数和整型会被转化为JSON里边的常规数字。
 字符串将以UTF-8编码转化输出为Unicode字符集的字符串，特殊字符比如<将会被转义为
\u003c。
 数组和切片会转化为JSON里边的数组，但[]byte类型的值将会被转化为 Base64 编码后
的字符串，slice类型的零值会被转化为 null。
 结构体会转化为JSON对象，并且只有结构体里边以大写字母开头的可被导出的字段才会
被转化输出，而这些可导出的字段会作为JSON对象的字符串索引。
 转化一个map类型的数据结构时，该数据的类型必须是 map[string]T（T可以是
encoding/json 包支持的任意数据类型）。

#### 解码JSON数据

要解码一段JSON数据，首先需要在Go中创建一个目标类型的实例对象，用于存放解码后的值：
var book Book 

假设一个JSON对象有个名为"Foo"的索引，要将"Foo"所对应的值填充到目标结构体的目标字段上，json.Unmarshal()将会遵循如下顺序进行查找匹配：
(1) 一个包含Foo标签的字段；
(2) 一个名为Foo的字段；
(3) 一个名为Foo或者Foo或者除了首字母其他字母不区分大小写的名为Foo的字段。
这些字段在类型声明中必须都是以大写字母开头、可被导出的字段。

#### 解码未知结构的JSON数据

每一个类型其实都至少实现了一个空接口，空接口是通用类型

如果要解码一段未知结构的JSON，只需将这段JSON数据解码输出到一个空接口即可

JSON数据里边的元素类型将做如下转换：

 JSON中的布尔值将会转换为Go中的bool类型；
 数值会被转换为Go中的float64类型；
 字符串转换后还是string类型；
 JSON数组会转换为[]interface{}类型；
 JSON对象会转换为map[string]interface{}类型；
 null值会转换为nil。

在Go的标准库encoding/json包中，允许使用map[string]interface{}和[]interface{}类型的值来分别存放未知结构的JSON对象或数组，

```go
//示例代码
b := []byte(`{ 
 "Title": "Go语言编程", 
 "Authors": ["XuShiwei", "HughLv", "Pandaman", "GuaguaSong", "HanTuo", "BertYuan", 
 "XuDaoli"], 
 "Publisher": "ituring.com.cn", 
 "IsPublished": true, 
 "Price": 9.99, 
 "Sales": 1000000 
}`) 
var r interface{} 
err := json.Unmarshal(b, &r) 
```

在上述代码中，r被定义为一个空接口。json.Unmarshal() 函数将一个JSON对象解码到空接口r中，最终r将会是一个键值对的 map[string]interface{} 结构：

```go
map[string]interface{}{ 
 "Title": "Go语言编程", 
 "Authors": ["XuShiwei", "HughLv", "Pandaman", "GuaguaSong", "HanTuo", "BertYuan", 
 "XuDaoli"], 
 "Publisher": "ituring.com.cn", 
 "IsPublished": true, 
 "Price": 9.99, 
 "Sales": 1000000 
} 
```

要访问解码后的数据结构，需要先判断目标结构是否为预期的数据类型：

```go
gobook, ok := r.(map[string]interface{}) 
```

然后，我们可以通过for循环搭配range语句一一访问解码后的目标数据：



