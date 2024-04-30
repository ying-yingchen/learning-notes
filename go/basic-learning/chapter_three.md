### chapter_three

#### 值语义和引用语义

二者差别在于赋值

```go
b = a //b的修改不会影响a的值
b.Modify()  //会影响a的值，此类为引用类型
```

值语义：byte,int,bool,float32,array,struct,pointer等

#### 结构struct

```go
type <Name> structz{}

type person struct{
   Name string
   Age  int
}
func main(){
   a := person{
       Name : "yuyan",
       Age :   27,
   }
   fmt.Println(a)
}
//输出{yuyan 27}

func main(){
   a := &person{
       Name : "yuyan",
       Age :   27,
   }
    a.Name = "yusinan"
    fmt.Println(a)
    A(a)
    fmt.Println(a)
}
func A(per *person){
    per.Age = 28
    fmt.Println("A",per)
}
//输出&{yusinan 27}; A &{yusinan 28}; &{yusinan 28} 
```

匿名结构

```go
package main()
import(
    "fmt"
)
type person struct {
    Name  string
    Age   int
    Contact struct{
        Phone,City string
    }
}
func main(){
    a := person{Name:"yuyan", Age:27}
    a.Contact.Phone = "52617290"
    a.Contact.City = "beijing"
    fmt.Println(a)
}
//输出{yuyan 27}{52617290 beijing}
```

匿名片段

```go
type human struct {
    Sex int
}
type teacher struct{
    human
    Name string
    Age int
}
type student struct{
    human
    Name string
    Age int
}
func main(){
    a := teacher{Name: "yuyan",Age: 27, human:human{Sex:1}}
    b := student{Name: "yusinan",Age: 18, human:human{Sex:0}}
    a.Name = "yuxiaoyu"
    a.Age = 28
    a.Sex = 526
    fmt.Println(a,b)
}
//输出{{526} yuxiaoyu 28}{{0} yusinan 18}
```

#### 方法method

通过显示说明receiver来实现与某个类型的组合

```go
type A struct {
    Name string
}
type B struct {
    Name string
}
func main(){
    a := A{}
    a.Print()
    b := B{}
    b.Print()
}
func (a A)Print(){
    fmt.Println("A")
}
func (b B)Print(){
    fmt.Println("B")
}
//输出A B
```

访问权限

```go
type A struct{
    name string
}
func main(){
    a := A{}
    a.Print()
}
func (a *A) Print(){
    a.name = "526"
    fmt.Println(a.name)
}
//输出526，方法的访问权限很高
```

#### 接口interface

一个或多个方法签名的集合，只有方法声明

```go
type  USB interface{
    Name() string
    Connect()
}    //接口赋值
type PhoneConnecter struct{
    name string
}
func (pc PhoneConnecter) Name() string{
    return pc.name
}
func (pc PhoneConnecter) Cinnect(){
    fmt.Println("Contect:",pc.name)
}
func main(){
    var a USB
    a = PhoneContecter{"PhoneContecter"}
    a.Connect()
}
//输出Connect:PhoneConnecter
```

判断类型用type switch

判断数据类型用ok pattern

Any类型：空接口interface{ }

#### 反射reflection

配合interface{}空接口可有更大地发挥余地

使用Type Of和Value Of函数从接口中获取目标对象信息

```go
type User struct{
    Id, Age int
    Name string
}
func (u User) Hello(){
    fmt.Println("Hello,world")
}
func main(){
    
}

func Info(o interface{}) {
    t := reflect.TypeOf(o)
    fmt.Println("Type:",t.name())
    
    v := reflect.ValueOf(O)
    fmt.Println("Fields:")
    
    for i := 0;i < t,NumField(); i++{
        f := t.Field(i)
        val := v.Field(i).Interfance
        fmt.Println("%6s: %v = %v".f.Name,f.Type,val)
    }
}
```

