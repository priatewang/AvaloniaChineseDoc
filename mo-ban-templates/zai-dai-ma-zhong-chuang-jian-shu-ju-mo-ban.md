# 在代码中创建数据模板

Avalonia也支持使用[`FuncDataTemplate<T>`](http://reference.avaloniaui.net/api/Avalonia.Controls.Templates/FuncDataTemplate\_1/)类在代码中创建数据模板。

简单来说，你可以通过将一个接受实例和`namescope`的lambda表达式传递给`FuncDataTemplate<T>`构造函数来创建一个数据模板：

```csharp
var template = new FuncDataTemplate<Student>((value, namescope) =>
    new TextBlock
    {
        [!TextBlock.TextProperty] = new Binding("FirstName"),
    });
```

它等价于以下XAML代码：

```markup
<DataTemplate DataType="{x:Type local:Student}">
    <TextBlock Text="{Binding FirstName}"/>
</DataTemplate>
```

## 示例

[FuncDataTemplate示例](https://github.com/AvaloniaUI/Avalonia.Samples/blob/main/src/Avalonia.Samples/DataTemplates/FuncDataTemplateSample)
