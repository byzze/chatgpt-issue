# 一个使用Golang开发的简单HTTP/2推送程序的示例代码
```go
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        pusher, ok := w.(http.Pusher)
        if ok {
            // 推送资源
            if err := pusher.Push("/style.css", nil); err != nil {
                fmt.Println("Failed to push:", err)
            }
        }
        // 返回HTML页面
        fmt.Fprintf(w, `
            <html>
                <head>
                    <link rel="stylesheet" href="/style.css">
                </head>
                <body>
                    <h1>Hello, HTTP/2 Push!</h1>
                </body>
            </html>
        `)
    })

    // 启动HTTP/2服务器
    server := &http.Server{
        Addr:    ":8080",
        Handler: nil,
    }
    fmt.Println("Server started on port 8080")
    server.ListenAndServeTLS("server.crt", "server.key")
}
```

这个程序会启动一个HTTP/2服务器，并在客户端请求根路径时推送一个CSS文件，然后返回一个包含标题的HTML页面。要运行这个程序，你需要在同一目录下创建一个名为`server.crt`和`server.key`的SSL证书和密钥文件。你可以使用以下命令生成自签名的SSL证书和密钥文件：

```bash
$ openssl req -x509 -newkey rsa:4096 -keyout server.key -out server.crt -days 365 -nodes
```

这个命令会生成一个有效期为365天的自签名SSL证书和密钥文件。