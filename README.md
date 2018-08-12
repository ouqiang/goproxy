# goproxy
Go HTTP(S)代理库, 支持中间人代理解密HTTPS

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

中间人代理, 解密HTTPS
---
系统需导入根证书 mitm-proxy.crt
```go
package main

import (
	"crypto/tls"
	"net/http"
	"sync"
	"time"

	"github.com/ouqiang/goproxy"
)
// 实现证书缓存接口
type Cache struct {
	m sync.Map
}

func (c *Cache) Set(host string, cert *tls.Certificate) {
	c.m.Store(host, cert)
}
func (c *Cache) Get(host string) *tls.Certificate {
	v, ok := c.m.Load(host)
	if !ok {
		return nil
	}

	return v.(*tls.Certificate)
}

func main() {
	proxy := goproxy.New(goproxy.WithDecryptHTTPS(&Cache{}))
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