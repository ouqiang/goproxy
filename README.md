# goproxy
Go HTTP(S)代理库

安装
----

```bash
go get github.com/ouqiang/goproxy
```

使用
----

```go
package main

import (
	"net/http"
	"time"

	"github.com/ouqiang/goproxy"
)

func main() {
	proxy := goproxy.New()
	server := &http.Server{
		Addr:         ":8080",
		Handler:      proxy,
		ReadTimeout:  1 * time.Minute,
		WriteTimeout: 1 * time.Minute,
	}
	err := server.ListenAndServe()
	if err != nil {
		panic(err)
	}
}
```

### 代理测试
```bash
curl -x localhost:8080 https://www.baidu.com
```
