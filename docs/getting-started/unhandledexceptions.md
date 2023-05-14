# 未处理的异常

Avalonia 不提供任何处理全局异常并将其标记为已处理的机制。原因是无法知道异常是否已被正确处理，因此应用程序可能处于无效状态。相反，强烈建议如果您的应用程序可以处理异常，则在本地处理异常。尽管如此，将任何未处理的异常记录到日志中以进行进一步的支持和调试仍然是一个好主意。

## 日志

我们建议将异常记录到控制台、文件或任何其他地方。有许多日志记录库可供选择，例如 [Serilog](https://serilog.net) 和 [NLog](https://nlog-project.org)。

## 全局 try-catch

您可以在 `Program.cs` 文件中捕获来自主线程（在 Avalonia 中也是 UI 线程）的任何异常。为此，我们只需将整个 `void Main` 包装在 `try` 和 `catch` 块中。在 `catch` 块中，您可以记录错误、通知用户、发送日志文件或重新启动应用程序。

```csharp
// 文件：Program.cs

public static void Main(string[] args)
{
    try
    {
        // 在此准备并运行您的 App
        BuildAvaloniaApp()
            .StartWithClassicDesktopLifetime(args);
    }
    catch (Exception e)
    {
        // 在此处我们可以使用异常，例如将其添加到我们的日志文件中
        Log.Fatal(e, "发生了严重错误");
    }
    finally
    {
        // 这个块是可选的。
        // 如果您需要清理东西或类似操作，请使用 finally 块
        Log.CloseAndFlush();
    }
}
```

## 另一个线程引发的异常

如果您使用 `Task` 异步运行一些工作，则可以设置 `TaskScheduler.UnobservedTaskException`。有关更多信息，请阅读[Microsoft .NET文档](https://docs.microsoft.com/dotnet/api/system.threading.tasks.taskscheduler.unobservedtaskexception)。

## 来自 Reactive UI 的异常

如果您将 Avalonia 与 [ReactiveUI](https://docs.avaloniaui.net/guides/basics/mvvm#frameworks) 一起使用，则可以订阅它们的 `RxApp.DefaultExceptionHandler`。有关更多信息，请参阅 [ReactiveUI 默认异常处理程序](https://www.reactiveui.net/docs/handbook/default-exception-handler/)。

请注意，`RxApp.DefaultExceptionHandler` 应在创建任何 ReactiveCommand 之前设置。否则，不会使用自定义处理程序。您可以在应用程序入口点或在创建任何 Avalonia 视图或窗口之前设置它。
