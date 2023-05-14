# 用户控件UserControls

`UserControl` 在 Avalonia 中代表一个 "视图"，是一个预定义布局的可重用控件集合。

一个 `UserControl` 通常由两部分组成：一个 XAML 文件（例如 `MyUserControl.axaml`）和一个代码后台文件（例如 `MyUserControl.axaml.cs`）。代码后台定义了一个代表控件的 .NET 类。

当使用 MVVM 模式时，`UserControl` 通常与 "视图模型" 配对使用。更多信息请参见[教程](https://docs.avaloniaui.net/tutorials/todo-list-app)。

您可以使用模板创建 `UserControl`：

### Visual Studio

1. 在“解决方案资源管理器”中右键单击要添加控件的文件夹
2. 选择“添加->新建项目”菜单项
3. 在弹出的对话框中，导航到类别树中的“Avalonia”部分
4. 选择 "UserControl (Avalonia)"
5. 在“名称”下输入您的控件名称
6. 单击“添加”按钮

### .NET Core CLI

使用以下命令替换 `[namespace]` 为您要在其中创建 `UserControl` 的命名空间，`[name]` 为控件名称。

```bash
dotnet new avalonia.usercontrol -p:n [namespace] -n [name]
```

有关更多信息，请参见[.NET core 模板存储库](https://github.com/AvaloniaUI/avalonia-dotnet-templates/)。
