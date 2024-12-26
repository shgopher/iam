# shgopher: 现在有两个pkg包，第一个是最外层的pkg，第二个是internal中的pkg，我要问你，外面的pkg是如何调用 internal中的的pkg的内容的，我要详细的调用过程

GitHub Copilot: 让我详细解析外部pkg如何使用internal/pkg中的内容：

### 1. 调用规则说明

内部pkg包的使用遵循Go的可见性规则：
- 

internal

包只能被同一父目录下的代码引用
- 外部pkg可以通过项目内部定义的接口调用internal/pkg

### 2. 调用示例

1. **日志组件调用**:
```go
import (
    "github.com/marmotedu/iam/internal/pkg/logger"  // 导入内部logger包
)

func Init(opts *Options) {
    // 使用internal/pkg/logger中的实现
    logger := logger.NewLogger(opts) 
}
```

2. **HTTP中间件调用**:
```go
import (
    "github.com/marmotedu/iam/internal/pkg/middleware"
)

func (a *App) InstallMiddlewares() {
    // 使用internal/pkg/middleware中的中间件
    a.Use(middleware.RequestID())
    a.Use(middleware.Context())
}
```

3. **错误码使用**:
```go
import (
    "github.com/marmotedu/iam/internal/pkg/code"
)

func (s *Storage) GetKey(key string) error {
    if key == "" {
        return code.ErrKeyNotFound // 使用internal定义的错误码
    }
    return nil
}
```

### 3. 常见调用关系

1. **配置管理**:
```go
pkg/app/config.go -> internal/pkg/options/feature.go
```

2. **验证功能**:
```go
pkg/validator -> internal/pkg/validation
```

3. **工具函数**:
```go
pkg/util -> internal/pkg/util/reflect
```

### 4. 调用链路示例

```
pkg/app/app.go 
  -> internal/pkg/server/config.go
    -> internal/pkg/options/feature.go
      -> internal/pkg/validation
```

这种设计模式允许：
1. 内部实现细节封装在internal包中
2. 对外提供稳定的API接口
3. 保持代码的模块化和可维护性
4. 控制包的访问权限

这样的分层设计有助于保持代码的清晰度和可维护性，同时也保护了内部实现细节。