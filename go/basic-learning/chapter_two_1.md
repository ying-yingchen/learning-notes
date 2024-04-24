### chapter two_1

### 变量  常量  类型

#### 单个变量的声明与赋值

变量的声明格式：var<变量名称><变量类型>

变量的赋值格式：<变量名称> = <表达式>

声明同时赋值：var<变量名称> [变量类型] = <表达式>

变量初始化：":="用于明确表达同时进行声明和初始化的工作，":="左侧的变量不可是已被声明的

```go
var {变量名称} {变量类型}
//指定变量类型且相对赋值，完整声明+初始化
var {变量名} {变量类型} = {变量值}
var v1 int = 10 
//提供初始化值，可省略变量类型
var {变量名} = {变量值}
var v2 = 10    
//适用于局部变量
{变量名} := {变量值}
v3 := 10   
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
//支持多重赋值功能（第三行）
var v10 int  
v10 = 526    //赋值
i,j = j,i
```

全局变量的声明不可省略var，但可以使用并行方式

所有变量都可以使用类型推断

局部变量不可使用var()方式简写，只能使用并行方式

#### 匿名变量

"_"特殊变量名，任何赋予它的值都会被丢弃

```go
_,_,nickName := GetName( )
```

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

#### 常量:已知不可改变的值

等号右侧必须是常量或常量表达式

常量表达式中的函数必须是内置函数，即常量的值在编译时就已经确定

```go
const {常量名} {常量类型} 或 const {常量名} = {常量值}
```

字面常量(literal)

```go
526     //整型
3.1415  //浮点型
5.2+6i  //复数类型
true    //布尔类型
"foo"   //字符串类型
```

常量定义：关键字const，常量或常量表达式

预定义常量：true,false是布尔类型，而iota是自增常量

iota：从0开始取值每出现一次，自身值加1，在每一个const出现时被重置为0

```go
//定义年龄
const age int = 27
//定义姓名及年龄（方法一）
const(
     name = "yuyan"
     age = 27
)
//定义姓名及年龄（方法二）
const name,age = "yuyan",27
//定义年龄及身高
const age,height int = 27,172

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

#### 枚举:一系列相关的常量

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

const (
    Sunday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday       //以大写字母开头的常量在包外可见，可被其他包访问
    numberOfDays   //此常量没有导出，以小写字母开头的常量为包内私有
)
```

#### 类型

#### Go基本类型

布尔类型：bool，长度1字节，取值范围：true和false

整型：int/uint，32/64

8位整型：int8/uint8,长度1字节，取值范围：-127~128/0到255

字节型：byte（uint8别名）

rune：int32别名，长度4字节

浮点型：float32/float64,长度4/8字节，小数位精确到7/15位

复数：complex64/complex128,长度8/16字节

足够保存指针的32位或64位整型数型：uintptr

其他值类型：array,struct,string

引用类型：slice,map,chan

接口类型：interface

函数类型：func

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

#### 整型：有符号整型和无符号整型

* ```go
  {符号位}{数字位置}
  ```

* |        | 类型   | 长度（字节数）          | 值的范围        |
  | :----: | ------ | ----------------------- | --------------- |
  | 有符号 | int    | 32位平台为4/64位平台为8 | int32/int64     |
  |        | int8   | 1                       | -128~127        |
  |        | int16  | 2                       | -32768~32678    |
  | 无符号 | uint   | 32位平台为4/64位平台为8 | uint32/uint64   |
  |        | uint8  | 1                       | 0~255(2^8-1)    |
  |        | uint16 | 2                       | 0~65535(2^16-1) |

* 不同类型的整型无法相互赋值，需类型转换，有的转换会丢失准确性

* %求余、^x取反、x ^ y异或

#### 浮点型：包含小数点的数据

* ```go
  {符号位}{指数位}{有效数字位}
  ```

  符号位：浮点数的正负性，在最高位，0正1负

  指数位：浮点数的数量级，带符号的整数，长度通常为 8 或 11 位

  有效数字位：浮点数的精度，带符号的小数

* float32相当于C的float类型，float64相当于C的double类型

  ```go
  var fvalue1 float32
  fvalue1 = 12
  fvalue2  := 12.0 //加小数点才会被推导为浮点型，将自动设为float64
  
  //强制类型转换
  fvalue1 = float32(fvalue2)
  ```

* 浮点数比较

  ```go
  import "math" 
  // p为用户自定义的比较精度，比如0.00001
  func IsEqua1(f1,f2,p float64) bool {
      return math.Fdim(f1, f2) < p
  }
  ```

#### 布尔型：关键字bool，true和false，只占用1bit，通常用于判断语句

```go
var v1 bool
v1 = true
v2 := (1 == 2) //v2也会被推导为bool类型
```

#### 复数:实部+虚部

``` 
var value1 complex64 //由2个float32构成的复数结构

value1 = 5.2 + 6i
value2 := 5.2 + 6i //value2是complex128类型
value3 := complex(5,2, 6i) //同2
```

#### 字符串

```go
var str string  //声明一个字符串变量
str = "Hello world" //字符串赋值
ch := str[0]  //取字符串的第一个字符
fmt,Println("The length of \"%s\" is %d \n", str, len(str))
fmt,Println("The first character of \"%s\" is %c.\n", str, ch)
//输出结果为The length of "Hello world" is 11
//        The first character of "Hello world" is H
```

字符串操作

字符串连接：x + y

字符串长度：len(s)

取字符：s[i]      "Hello"[1]   //输出为"e"

字符串遍历：字节数组（byte类型）、Unicode字符（rune类型）

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

```go
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

