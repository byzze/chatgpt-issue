# gin在web服务中http处理和websocket处理怎么共用一个端口程序写法
In a Gin web server, you can handle both HTTP and WebSocket requests on the same port by using the github.com/gin-gonic/gin package and the github.com/gorilla/websocket package.

Here's an example of how you can handle both HTTP and WebSocket requests on the same port:

```go
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/gorilla/websocket"
    "net/http"
)

var upgrader = websocket.Upgrader{
    ReadBufferSize:  1024,
    WriteBufferSize: 1024,
}

func main() {
    router := gin.Default()

    // Handle HTTP requests
    router.GET("/", func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{
            "message": "Hello, World!",
        })
    })

    // Handle WebSocket requests
    router.GET("/ws", func(c *gin.Context) {
        conn, err := upgrader.Upgrade(c.Writer, c.Request, nil)
        if err != nil {
            c.JSON(http.StatusInternalServerError, gin.H{
                "message": "Failed to upgrade to WebSocket",
            })
            return
        }
        defer conn.Close()

        for {
            _, message, err := conn.ReadMessage()
            if err != nil {
                break
            }
            conn.WriteMessage(websocket.TextMessage, message)
        }
    })

    router.Run(":8080")
}
```
In the example above, we create a Gin router and define two routes: one for handling HTTP requests (/) and another for handling WebSocket requests (/ws). When a client sends an HTTP request to the server, the / route will return a JSON response with a message "Hello, World!". When a client sends a WebSocket request to the server, the /ws route will upgrade the HTTP connection to a WebSocket connection and start listening for incoming messages. Any message received by the server will be sent back to the client.

The Upgrader variable is used to upgrade the HTTP connection to a WebSocket connection. This variable sets the buffer size for reading and writing WebSocket messages.

Finally, we run the Gin router on port :8080 by calling the Run method. This will start listening for incoming HTTP and WebSocket requests on the same port.