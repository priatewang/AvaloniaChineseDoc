# 数据绑定

Avalonia提供了与控件和任意.NET对象之间的绑定的全面支持。数据绑定可以在XAML或代码中设置，并支持以下功能：

* 多种绑定模式：单向、双向、一次性和单向到源
* 绑定到`DataContext`
* 绑定到其他控件
* 绑定到`Tasks和Observables`
* 绑定转换器和否定绑定值

以下示例显示了一个与绑定相关联的`TextBox`禁用时的`TextBlock`，可以使用绑定来实现：

```csharp
<StackPanel>
    <TextBox Name="input" IsEnabled="False"/>
    <TextBlock IsVisible="{Binding !#input.IsEnabled}">Sorry, no can do!</TextBlock>
</StackPanel>
```

在此示例中，使用`#input.IsEnabled`将绑定设置到`input`控件的`IsEnabled`属性，并将该绑定的值取反并提供给`TextBlock.IsVisible`属性。
