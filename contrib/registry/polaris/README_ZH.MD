# GoFrame Polaris Registry

[English](README.MD) | 简体中文

使用`PolarisMesh`作为服务注册、发现管理和心跳上报。

## 安装

```
go get -u -v github.com/gogf/gf/contrib/registry/polaris/v2
```

suggested using `go.mod`:

```
require github.com/gogf/gf/contrib/registry/polaris/v2 latest
```

## Golang版本限制

```
golang version >= 1.15
```

## 示例

### 引用示例

[服务端](example/registry/polaris/server/main.go)

```go
package main

import (
    "github.com/polarismesh/polaris-go/pkg/config"
    
    "github.com/gogf/gf/contrib/registry/polaris/v2"
    "github.com/gogf/gf/v2/frame/g"
    "github.com/gogf/gf/v2/net/ghttp"
    "github.com/gogf/gf/v2/net/gsvc"
)

func main() {
    conf := config.NewDefaultConfiguration([]string{"183.47.111.80:8091"})
    
    // TTL egt 2*time.Second
    gsvc.SetRegistry(polaris.NewRegistryWithConfig(conf, polaris.WithTTL(10)))
    
    s := g.Server(`hello.svc`)
    s.BindHandler("/", func(r *ghttp.Request) {
        g.Log().Info(r.Context(), `request received`)
        r.Response.Write(`Hello world`)
    })
    s.Run()
}

```

[客户端](example/registry/polaris/client/main.go)

```go
package main

import (
    "fmt"
    "time"
    
    "github.com/polarismesh/polaris-go/pkg/config"
    
    "github.com/gogf/gf/contrib/registry/polaris/v2"
    
    "github.com/gogf/gf/v2/frame/g"
    "github.com/gogf/gf/v2/net/gsvc"
    "github.com/gogf/gf/v2/os/gctx"
)

func main() {
    conf := config.NewDefaultConfiguration([]string{"183.47.111.80:8091"})
    
    gsvc.SetRegistry(polaris.NewRegistryWithConfig(conf, polaris.WithTTL(10)))
    
    for i := 0; i < 100; i++ {
        res, err := g.Client().Get(gctx.New(), `http://hello.svc/`)
        if err != nil {
            panic(err)
        }
        fmt.Println(res.ReadAllString())
        res.Close()
        time.Sleep(time.Second)
    }
}

```

## 协议

`GoFrame Polaris` 使用非常友好的 [MIT](../../../LICENSE) 开源协议进行发布，永久`100%`开源免费。