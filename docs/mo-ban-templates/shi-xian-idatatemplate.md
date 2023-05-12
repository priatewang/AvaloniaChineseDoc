# 实现 IDataTemplate

## 实现 IDataTemplate 以满足高级场景需求

如果你需要对 `DataTemplate` 进行更多的控制，可以创建一个实现 `IDataTemplate` 接口的类。通过这个接口，你不仅可以根据数据的 `DataType` 匹配模板，还可以根据其属性或其他需求来创建自己的 `DataTemplate`。

要使用这个接口，你必须在你的类中实现以下两个成员：

* `public bool Match(object data) { ... }` 在这个方法中，你需要检查提供的数据是否与你的 `IDataTemplate` 匹配。如果匹配，就需要返回 `true`，否则返回 `false`。
* `public IControl Build(object param) { ... }` 在这个方法中，你需要构建并返回代表数据的控件。

## 示例

### 基本示例

下面是一个非常基本的 `IDataTemplate`-interface 实现示例：

```csharp
// 记得导入所需的命名空间
// using Avalonia.Controls.Templates;

public class MyDataTemplate : IDataTemplate
{
    public IControl Build(object param)
    {
        // 构建要显示的控件
        return new TextBlock() { Text = (string)param };
    }

    public bool Match(object data)
    {
        // 检查是否可以接受提供的数据
        return data is string;
    }
}
```

现在你可以在视图中使用 `MyDataTemplate` 类，如下所示：

```markup
<!-- 记得在视图中添加所需的前缀 -->
<!-- xmlns:dataTemplates="using:MyApp.DataTemplates" -->

<ContentControl Content="{Binding MyContent}">
	<ContentControl.ContentTemplate>
		<dataTemplates:MyDataTemplate />
	</ContentControl.ContentTemplate>
</ContentControl>
```

### 高级示例

[实现 IDataTemplate](https://github.com/AvaloniaUI/Avalonia.Samples/tree/main/src/Avalonia.Samples/DataTemplates/IDataTemplateSample)
