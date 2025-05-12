# GEventSource

GEventSource 是一个用于处理 Server-Sent Events (SSE) 的客户端库，能够轻松地与 SSE 服务端建立连接并接收实时事件流。

## 特性

- 连接到 SSE 服务端，并自动解析事件数据
- 自定义配置，比如是否在断开连接时自动重连
- 支持处理标准的 SSE 事件：open、message、comment、error 和 complete
- 易于集成，自定义回调处理逻辑

## 安装

通过 Swift Package Manager 集成：
1. 在 Xcode 的 "File" -> "Swift Packages" -> "Add Package Dependency" 中，
2. 输入 GEventSource 仓库的 URL，
3. 选择适合的版本后将其添加到项目中。

或者在 Package.swift 中添加依赖：
```
  dependencies: [
      .package(url: "https://your-repository-url/GEventSource.git", from: "1.0.0")
  ]
```

## 使用示例

下面的代码展示了如何使用 GEventSource 来创建 SSE 连接，并处理来自服务端的事件：

```swift
import Foundation

// 定义一个继承自 EventSource.EventHandler 的处理类
class MyEventHandler: EventSource.EventHandler {
    // 连接建立成功时调用
    func onOpen(eventSource: EventSource) {
        print("连接已打开")
    }

    // 服务端发送消息时调用
    func onMessage(eventSource: EventSource, event: EventSource.MessageEvent) {
        print("接收到消息：\(event.event) - \(event.data)")
    }
    
    // 接收到注释信息时调用
    func onComment(eventSource: EventSource, comment: String) {
        print("接收到注释：\(comment)")
    }
    
    // 发生错误时调用
    func onError(eventSource: EventSource, error: Error) {
        print("发生错误：\(error.localizedDescription)")
    }
    
    // 连接关闭时调用（error 参数非 nil 表示包含错误信息）
    func onComplete(eventSource: EventSource, error: Error?) {
        if let error = error {
            print("连接已关闭，错误：\(error.localizedDescription)")
        } else {
            print("连接已正常关闭")
        }
    }
}

// 创建配置和事件源
let url = URL(string: "https://example.com/sse")!
var configuration = EventSource.Configuration(url: url)
configuration.shouldRetryOnDisconnect = false // 设置为不自动重连
let eventHandler = MyEventHandler()

let eventSource = EventSource(configuration: configuration, eventHandler: eventHandler)
eventSource.connect()

```

## 配置说明

- URL：必填，用于指定 SSE 服务端地址。
- shouldRetryOnDisconnect：是否在连接断开后自动进行重连，默认为 true；如果设置为 false，则在断线后不会重连。
- 其他配置参数可参考 [Configuration]。

## 事件处理

GEventSource 定义的事件回调方法说明如下：

- onOpen(eventSource:): 当连接成功建立时调用。
- onMessage(eventSource:event:): 每当接收到消息事件时调用，MessageEvent 包含事件类型和数据。
- onComment(eventSource:comment:): 当接收到注释（以冒号开头）时调用。
- onError(eventSource:error:): 当连接过程中发生错误时调用。
- onComplete(eventSource:error:): 当连接关闭时调用；如果 error 不为 nil，则说明关闭时发生错误。

开发者可以根据业务需求实现自定义逻辑来处理这些事件。


## 注意事项

- 使用 SSE 时需要保证服务端支持此协议。
- 根据网络情况及应用场景，合理配置自动重连策略。
- 断线重连时，开发者可添加断线记录或重连次数限制，避免循环重连引发负荷问题。

## 贡献

我们欢迎各种形式的贡献！  
如果你有新的特性建议或发现bug，欢迎提交 Issue 或 Pull Request。  

## 许可证

本项目遵循 [MIT 许可证](LICENSE) 发布。

