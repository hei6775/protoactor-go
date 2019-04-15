[![Go Report Card](https://goreportcard.com/badge/github.com/AsynkronIT/protoactor-go)](https://goreportcard.com/report/github.com/AsynkronIT/protoactor-go)
[![GoDoc](https://godoc.org/github.com/AsynkronIT/protoactor-go?status.svg)](https://godoc.org/github.com/AsynkronIT/protoactor-go)
[![Build Status](https://travis-ci.org/AsynkronIT/protoactor-go.svg?branch=dev)](https://travis-ci.org/AsynkronIT/protoactor-go)
[![Coverage Status](https://coveralls.io/repos/github/AsynkronIT/protoactor-go/badge.svg?branch=dev)](https://coveralls.io/github/AsynkronIT/protoactor-go?branch=dev)
[![Sourcegraph](https://sourcegraph.com/github.com/AsynkronIT/protoactor-go/-/badge.svg)](https://sourcegraph.com/github.com/AsynkronIT/protoactor-go?badge)

[![Join the chat at https://gitter.im/AsynkronIT/protoactor](https://badges.gitter.im/AsynkronIT/protoactor.svg)](https://gitter.im/AsynkronIT/protoactor?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

# 跨平台的 actors

介绍支持跨平台的 actor 在 Go 和 C＃中。

我可以用这个吗？
Go 的实现仍处于测试阶段，有用户已经在生产中使用 Proto Actor for Go。但是请注意，API 可能会随着时间的推移而发生变化，直到 1.0。

## 源码 - Go

这是 Proto Actor 的 Go 库。

C#的实现在这里可以找到 [https://github.com/AsynkronIT/protoactor-dotnet](https://github.com/AsynkronIT/protoactor-dotnet)

## 设计原则:

**简约的 API** -
API 应该小而且易于使用。
避免企业级的 JVM 喜欢容器和配置的。

**以现有技术为基础** - 现在已经有许多伟大的技术，例如网络和集群，建立在这些基础上。
例如 用于网络通讯的 gRPC 流，用于集群的 Consul.IO。

**传递数据，而不是对象** - 序列化是一个明确的重点，不要试图隐藏它。
Protobuf 在这个方向上一直努力。

**快** - 不要为有魔力的 API 技巧去交换你的性能。

超快速的远程处理，Proto Actor 目前设法仅使用两个 actor 在两个节点之间**每秒传递超过两百万条消息**，同时仍然保留消息顺序！ 这是 Scala Akka 新的超级高级 UDP 基于动脉传输速度的六倍，比 Akka.NET 快 30 倍。

```text
:> node1.exe
2016/12/02 14:30:09 50000
2016/12/02 14:30:09 100000
2016/12/02 14:30:09 150000
... snip ...
2016/12/02 14:30:09 900000
2016/12/02 14:30:09 950000
2016/12/02 14:30:10 1000000
2016/12/02 14:30:10 Elapsed 999.9985ms
2016/12/02 14:30:10 Msg per sec 2000003 <--
```

## 历史

作为 Akka.NET 项目的创建者，我在参与该项目时得出了一些明显的结论。 在 Akka.NET 中，我们创建了自己的线程池，我们自己的网络层，我们自己的序列化支持，我们自己的配置支持等等。这是有趣和具有挑战性的，但现在我坚定认为，这是错误的方式。

**如果可能的话，软件应该通过组成构成，而不是构建**，只添加代码将现有的部分粘合在一起。
这产生了更好的上市时间，并使我们能够专注于解决手头的实际问题，在这种情况下才是并发和分布式编程。

Proto Actor 建立于已经有的技术，Protobuf 用于序列化，gRPC 流用于网络传输。
这确保了跨平台的兼容性，网络协议的版本差异和经过验证的稳定性。

另一个非常重要的因素是业务敏捷性和退出策略。 通过跨平台，如果您要从.NET 迁移到 Go，您的组织将不再绑定到特定平台，这可以在仍允许基于 actor 的服务在平台之间进行通信的同时完成。

通过不重塑来重塑。

---

## 为什么使用 Actors

![batman](/resources/batman.jpg)

- 解耦并发
- 默认分发
- 容错性能

有关 Actors 和 CSP 模式的差异的更深入描述，请参阅此主题 [Actors vs. CSP](https://www.quora.com/Go-programming-language-How-are-Akka-actors-are-different-than-Goroutines-and-Channels)

## Building

您需要确保正确的设置`$GOPATH`变量。

接下来，安装[standard protocol buffer implementation](https://github.com/google/protobuf)并运行以下命令以获取所有必需的工具：
Next, install the [standard protocol buffer implementation](https://github.com/google/protobuf) and run the following commands to get all the necessary tooling:

```
go get github.com/AsynkronIT/protoactor-go/...
cd $GOPATH/src/github.com/AsynkronIT/protoactor-go
go get ./...
make
```

调用最后一个命令后，您将生成 protobuf 定义并构建项目。

Windows 用户可以使用 Cygwin 来运行 make：[www.cygwin.com]（https://www.cygwin.com/）

## Testing

除了 consul 集成测试（您需要使用 consul 来运行这些测试）之外，此命令还会执行库中的所有测试。
我们还跳过不包含任何测试的目录。

```
go test `go list ./... | grep -v consul` | grep -v 'no test files'
```

If everything is ok, you will get the output:

```
ok  	github.com/AsynkronIT/protoactor-go/actor	0.115s
ok  	github.com/AsynkronIT/protoactor-go/eventstream	0.020s
ok  	github.com/AsynkronIT/protoactor-go/internal/queue/goring	2.524s
ok  	github.com/AsynkronIT/protoactor-go/internal/queue/mpsc	2.385s
ok  	github.com/AsynkronIT/protoactor-go/log	0.017s
ok  	github.com/AsynkronIT/protoactor-go/mailbox	2.742s
ok  	github.com/AsynkronIT/protoactor-go/plugin	1.227s
ok  	github.com/AsynkronIT/protoactor-go/router	1.836s
ok  	github.com/AsynkronIT/protoactor-go/stream	0.017s
```

## Hello world

```go
type Hello struct{ Who string }
type HelloActor struct{}

func (state *HelloActor) Receive(context actor.Context) {
    switch msg := context.Message().(type) {
    case Hello:
        fmt.Printf("Hello %v\n", msg.Who)
    }
}

func main() {
    context := actor.EmptyRootContext
    props := actor.PropsFromProducer(func() actor.Actor { return &HelloActor{} })
    pid, err := context.Spawn(props)
    if err != nil {
        panic(err)
    }
    pid.Tell(Hello{Who: "Roger"})
    console.ReadLine()
}
```
