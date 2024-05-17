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
