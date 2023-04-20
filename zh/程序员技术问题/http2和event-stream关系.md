# http/2和event-stream解释
`text/event-stream` 是一种基于 HTTP/1.1 的服务器推送技术，它允许服务器向客户端推送实时事件流。而 HTTP/2 也提供了类似的服务器推送功能，可以在单个连接上同时推送多个资源。

HTTP/2 推送的原理是，当客户端请求一个资源时，服务器可以主动推送与该资源相关的其他资源，以提高页面加载速度。服务器可以在响应头中添加 `Link` 字段，指示客户端需要推送的资源。客户端可以选择接受或拒绝这些推送资源。

而 `text/event-stream` 的原理是，客户端通过向服务器发送一个长连接请求，服务器会保持连接打开，并向客户端推送实时事件流。客户端可以通过监听 `message` 事件来接收服务器推送的事件。

虽然 `text/event-stream` 和 HTTP/2 推送都可以实现服务器向客户端推送实时事件，但它们的实现方式不同。`text/event-stream` 基于长连接，而 HTTP/2 推送基于单个连接上的多路复用。因此，它们的适用场景也不同。如果您需要推送大量的实时事件，`text/event-stream` 可能更适合您。如果您需要推送大量的静态资源，HTTP/2 推送可能更适合您。



## event-stream 推送例子
当使用 Golang 语言时，您可以使用 `net/http` 包中的 `http.ResponseWriter` 接口来实现 `text/event-stream`。以下是一个简单的示例代码：

```go
package main

import (
    "fmt"
    "net/http"
    "time"
)

func main() {
    http.HandleFunc("/events", func(w http.ResponseWriter, r *http.Request) {
        // 设置响应头
        w.Header().Set("Content-Type", "text/event-stream")
        w.Header().Set("Cache-Control", "no-cache")
        w.Header().Set("Connection", "keep-alive")

        // 向客户端发送事件流
        for i := 0; i < 10; i++ {
            fmt.Fprintf(w, "event: message\ndata: %s\n\n", time.Now().Format("2006-01-02 15:04:05"))
            w.(http.Flusher).Flush()
            time.Sleep(1 * time.Second)
        }
    })

    http.ListenAndServe(":8080", nil)
}
```

在上面的代码中，我们定义了一个 `/events` 路由，用于向客户端推送实时事件流。我们首先设置响应头，包括 `Content-Type`、`Cache-Control` 和 `Connection`。然后，我们使用 `fmt.Fprintf` 方法向客户端发送事件流，其中 `event` 字段表示事件类型，`data` 字段表示事件数据。我们还使用 `http.Flusher` 接口来刷新响应流，以确保事件流能够实时推送到客户端。最后，我们使用 `time.Sleep` 方法来模拟事件流的实时推送。

您可以使用浏览器访问 `http://localhost:8080/events`，然后在浏览器控制台中查看事件流。

## evnet-stream 推送2
```go
package main

import (
	"fmt"
	"net/http"
	"time"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, `
        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="UTF-8">
            <title>Server-Sent Events Demo</title>
        </head>
        <body>
            <h1>Server-Sent Events Demo</h1>
            <div id="news"></div>
            <script>
                var source = new EventSource("/news");
                source.onmessage = function(event) {
                    var news = JSON.parse(event.data);
                    var newsElem = document.createElement("div");
                    newsElem.innerHTML = "<h2>" + news.title + "</h2>";
                    document.getElementById("news").appendChild(newsElem);
                };
                setTimeout(function() {
                    source.close();
                }, 10000);
            </script>
        </body>
        </html>
    `)
}

func newsHandler(w http.ResponseWriter, r *http.Request) {

	// 设置响应头
	w.Header().Set("Content-Type", "text/event-stream")
	w.Header().Set("Cache-Control", "no-cache")
	w.Header().Set("Connection", "keep-alive")

	// 推送新闻数据
	for i := 0; i < 10; i++ {
		time.Sleep(1 * time.Second)
		news := fmt.Sprintf(`{"title": "News %d"}`, i+1)
		fmt.Fprintf(w, "data: %s\n\n", news)
		w.(http.Flusher).Flush()
	}
}

func main() {
	http.HandleFunc("/", handler)
	http.HandleFunc("/news", newsHandler)
	http.ListenAndServe(":8080", nil)
}
```