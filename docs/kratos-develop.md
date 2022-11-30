# Kratos 开发



## goland开发环境

### 使用Kratos CLI

```shell
go install github.com/go-kratos/kratos/cmd/kratos/v2@latest
go env -w GO111MODULE=on
# 创建项目，类似spring-boot-starter, 可以建好基本的项目目录结构
# kratos new <项目> -r <源> -b <分支>  --nomod
kratos new interface
kratos new interface -r https://gitee.com/go-kratos/kratos-layout.git	# 速度慢的话选择这个
# 安装 protoc protoc-gen-go, 根据操作系统选择压缩包
# protoc 是 proto 文件的编译器，详细参考：https://developers.google.com/protocol-buffers/docs/gotutorial
# protobuf3语法：https://developers.google.com/protocol-buffers/docs/proto3
wget https://github.com/protocolbuffers/protobuf/releases/download/v21.9/protoc-21.9-linux-x86_64.zip
wget https://github.com/protocolbuffers/protobuf-go/releases/download/v1.28.1/protoc-gen-go.v1.28.1.linux.amd64.tar.gz
# 丢到了/opt
export PATH=/opt/protoc-21.9-linux-x86_64/bin:$PATH
export PATH=/opt/protoc-gen-go:$PATH
export C_INCLUDE_PATH=/opt/protoc-21.9-linux-x86_64/include:$C_INCLUDE_PATH
source ~/.zshrc
# 查看include路径是否生效
cpp -v
# /opt/protoc-21.9-linux-x86_64/include	# 看到这句成功
# .
# /usr/lib/gcc/x86_64-linux-gnu/7/include
# /usr/local/include
# /usr/lib/gcc/x86_64-linux-gnu/7/include-fixed
# /usr/include/x86_64-linux-gnu
# /usr/include
```

比如这里创建商城项目:

```shell
kratos new mall-interface
kratos new mall-service-user
kratos new mall-service-goods
kratos new mall-service-order
kratos new mall-service-cart
kratos new mall-admin

# 目录建好后下载下依赖
go mod download / tidy

```



## [项目结构](https://go-kratos.dev/docs/intro/layout)

<img src="https://go-kratos.dev/images/ddd.png" style="zoom: 50%;" />

项目目录节点（如果是微服务，每个微服务模块都有这样一套目录）：

```
  .
├── Dockerfile  
├── LICENSE
├── Makefile  
├── README.md
├── api // 下面维护了微服务使用的proto文件以及根据它们所生成的go文件
│   └── helloworld
│       └── v1
│           ├── error_reason.pb.go
│           ├── error_reason.proto
│           ├── error_reason.swagger.json
│           ├── greeter.pb.go
│           ├── greeter.proto
│           ├── greeter.swagger.json
│           ├── greeter_grpc.pb.go
│           └── greeter_http.pb.go
├── cmd  // 整个项目启动的入口文件
│   └── server
│       ├── main.go
│       ├── wire.go  // 我们使用wire来维护依赖注入
│       └── wire_gen.go
├── configs  // 这里通常维护一些本地调试用的样例配置文件
│   └── config.yaml
├── generate.go
├── go.mod
├── go.sum
├── internal  // 该服务所有不对外暴露的代码，通常的业务逻辑都在这下面，使用internal避免错误引用
│   ├── biz   // 业务逻辑的组装层，类似 DDD 的 domain 层，data 类似 DDD 的 repo，而 repo 接口在这里定义，使用依赖倒置的原则。
│   │   ├── README.md
│   │   ├── biz.go
│   │   └── greeter.go
│   ├── conf  // 内部使用的config的结构定义，使用proto格式生成
│   │   ├── conf.pb.go
│   │   └── conf.proto
│   ├── data  // 业务数据访问，包含 cache、db 等封装，实现了 biz 的 repo 接口。我们可能会把 data 与 dao 混淆在一起，data 偏重业务的含义，它所要做的是将领域对象重新拿出来，我们去掉了 DDD 的 infra层。
│   │   ├── README.md
│   │   ├── data.go
│   │   └── greeter.go
│   ├── server  // http和grpc实例的创建和配置
│   │   ├── grpc.go
│   │   ├── http.go
│   │   └── server.go
│   └── service  // 实现了 api 定义的服务层，类似 DDD 的 application 层，处理 DTO 到 biz 领域实体的转换(DTO -> DO)，同时协同各类 biz 交互，但是不应处理复杂逻辑
│       ├── README.md
│       ├── greeter.go
│       └── service.go
└── third_party  // api 依赖的第三方proto
    ├── README.md
    ├── google
    │   └── api
    │       ├── annotations.proto
    │       ├── http.proto
    │       └── httpbody.proto
    └── validate
        ├── README.md
        └── validate.proto
```

