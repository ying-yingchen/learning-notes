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