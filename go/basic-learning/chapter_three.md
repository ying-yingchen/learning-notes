### chapter_three

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

