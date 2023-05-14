# 记录错误和警告

Avalonia可以使用`System.Diagnostics.Trace`记录警告和错误。要启用日志记录，`Program.cs`文件中应该包含`LogToTrace`方法调用：

```csharp
public static AppBuilder BuildAvaloniaApp()
    => AppBuilder.Configure<App>()
        .UsePlatformDetect()
        .LogToTrace();
```

默认情况下，此日志记录设置将将严重性为“警告”或更高的日志消息写入`System.Diagnostics.Trace`。可以通过向`LogToTrace()`传递一个`level`参数来控制严重性。

默认情况下，这些跟踪消息将被记录到您的IDE输出窗口。如果要将这些消息重定向到不同位置，请使用[`System.Diagnostics.Trace`](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.trace)提供的API。

### 区域

每个Avalonia日志消息都有一个“区域”，可用于过滤日志，只包括您感兴趣的事件类型。这些由`Avalonia.Logging.LogArea`静态类的成员描述，当前为：

* `Property`
* `Binding`
* `Animations`
* `Visual`
* `Layout`
* `Control`

`LogToTrace`方法允许指定要记录哪些区域：

```csharp
public static AppBuilder BuildAvaloniaApp()
    => AppBuilder.Configure<App>()
        .UsePlatformDetect()
        .LogToTrace(LogEventLevel.Debug, LogArea.Property, LogArea.Layout);
```
