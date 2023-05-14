# 应用程序生命周期

不同的平台之间，生命周期管理是不相同的。在 Windows Forms 和 WPF 中常用的生命周期管理只适用于桌面式平台。AvaloniaUI 是一个跨平台的框架，为了帮助你的应用程序具有可移植性，我们提供了不同的应用程序生命周期模型，并允许你在平台允许的情况下手动控制一切。

## 生命周期如何工作？

在桌面平台上，初始化应用程序的首选方法是：

```csharp
class Program
{
  // 这个方法是为了 IDE 预览器基础设施而需要的
  public static AppBuilder BuildAvaloniaApp() 
    => AppBuilder.Configure<App>().UsePlatformDetect();

  // 入口点。在此时应用程序还没有准备好，因此在这个点上
  // 你不能使用任何 Avalonia 类型或任何需要同步上下文准备好的东西。
  public static int Main(string[] args) 
    => BuildAvaloniaApp().StartWithClassicDesktopLifetime(args);
}
```

那么，主窗口设置在哪里呢？现在已经移动到了 Application 类中：

```csharp
public override void OnFrameworkInitializationCompleted()
{
  if (ApplicationLifetime is IClassicDesktopStyleApplicationLifetime desktop)
    desktop.MainWindow = new MainWindow();
  else if (ApplicationLifetime is ISingleViewApplicationLifetime singleView)
    singleView.MainView = new MainView();
  base.OnFrameworkInitializationCompleted();
}
```

这个方法在框架准备好使用并且 `ApplicationLifetime` 属性包含所选择的生命周期时被调用。如果你的应用程序在 IDE 预览器进程中以设计模式运行，`ApplicationLifetime` 就是 `null`。

## 生命周期类型

一个特定的生命周期可能会提供不同的方面，因此我们有一组接口来访问它们。

### IControlledApplicationLifetime

提供者：

* `StartWithClassicDesktopLifetime`
* `StartLinuxFramebuffer`

允许您订阅 `Startup` 和 `Exit` 事件，并通过调用 `Shutdown` 方法明确地关闭应用程序。还提供了应用程序的退出代码的控制。

### IClassicDesktopStyleApplicationLifetime

继承自：`IControlledApplicationLifetime`

提供者：

* `StartWithClassicDesktopLifetime`

允许您以 WindowsForms/WPF 的方式控制应用程序生命周期。提供了一种访问当前打开的窗口列表、设置主窗口以及 3 种关闭模式的方法：

* OnLastWindowClose - 当最后一个窗口关闭时关闭应用程序
* OnMainWindowClose - 当主窗口关闭时关闭应用程序，如果已经设置了主窗口
* OnExplicitShutdown - 禁用应用程序的自动关闭，需要手动调用 Shutdown

### ISingleViewApplicationLifetime

提供者：

* `StartLinuxFramebuffer`
* 移动平台（WIP）

一些平台没有桌面 `Window` 的概念，只允许同时在屏幕上显示一个视图。对于这种平台，这种生命周期允许您设置和更改 `MainView`。目前我们没有提供自己的导航栈实现，但您可以使用来自 ReactiveUI 的导航栈实现。
