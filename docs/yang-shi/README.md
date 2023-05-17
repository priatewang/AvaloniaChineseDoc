# 🎨 样式

Avalonia中的样式用于在控件之间共享属性设置。Avalonia样式系统可以看作是CSS样式和WPF/UWP样式的混合。在其最基本的形式中，一个样式由一个\_选择器\_和一组\_属性设置器\_组成。

下面的样式选择在`Window`中的任何具有`h1` \_样式类\_的`TextBlock`，并将其字体大小设置为24点，字体粗细设置为粗体：

```markup
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Window.Styles>
        <Style Selector="TextBlock.h1">
            <Setter Property="FontSize" Value="24"/>
            <Setter Property="FontWeight" Value="Bold"/>
        </Style>
    </Window.Styles>

    <TextBlock Classes="h1">我是一个标题！</TextBlock>
</Window>
```
