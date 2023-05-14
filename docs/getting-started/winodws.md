# 窗口Winodws

`Window` 是 Avalonia 中的顶级控件。

窗口由两部分组成：一个 XAML file（例如 `MainWindow.axaml`）和一个 [代码后台](https://docs.avaloniaui.net/guides/basics/code-behind) 文件（例如 `MainWindow.axaml.cs`）。代码后台定义了一个代表窗口的 .NET 类。

有关更多信息和示例，请参见 [`Window`](https://docs.avaloniaui.net/docs/controls/window) 控件。

默认应用程序模板创建一个名为 `MainWindow` 的单个窗口。您也可以从模板创建其他窗口：

### Visual Studio

* 1.右键单击您想要添加窗口的文件夹
* 2.选择 `Add -> New Item` 菜单项
* 3.在出现的对话框中，导航到类别树中的 "Avalonia" 部分
* 4.选择 "Window (Avalonia)"
* 5.在 "Name" 下输入窗口名称
* 6.单击 "Add" 按钮

### .NET Core CLI

运行此命令，将 `[namespace]` 替换为您想要在其中创建窗口的命名空间，将 `[name]` 替换为窗口的名称。

```PowerShell
dotnet new avalonia.window -na [namespace] -n [name]
```

有关更多信息，请参见 [.NET core 模板存储库](https://github.com/AvaloniaUI/avalonia-dotnet-templates/)。
