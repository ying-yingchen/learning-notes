### chapter_four_Simple API services

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
)

type User struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

func main() {
    http.HandleFunc("/users", getUserHandler)

    log.Fatal(http.ListenAndWrite(":8080", nil))
}

func getUserHandler(w http.ResponseWriter, r *http.Request) {
    user := User{
        Name: "John Doe",
        Age: 30,
    }

    jsonData, err := json.Marshal(user)
    if err!= nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }

    w.Header().Set("Content-Type", "application/json")
    w.Write(jsonData)
}
```

在这个示例中，定义了一个`User`结构体，然后在`/users`路由上处理请求，返回一个包含用户信息的 JSON 数据。

