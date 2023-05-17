# 样式

Avalonia中的样式用于在控件之间共享属性设置。Avalonia的样式系统可以看作是CSS样式和WPF/UWP样式的混合。样式的基本结构由一个\_选择器\_和一组\_属性设置器\_组成。

样式适用于定义它的控件及其所有子控件。

以下样式选择任何具有`h1` \_样式类\_的`TextBlock`，并将其字体大小设置为24点，字体粗细设置为粗体：

```markup
<Style Selector="TextBlock.h1">
    <Setter Property="FontSize" Value="24"/>
    <Setter Property="FontWeight" Value="Bold"/>
</Style>
```

样式可以在任何控件或`Application`对象上定义，通过将其添加到[`Control.Styles`](http://reference.avaloniaui.net/api/Avalonia/StyledElement/0A46A84A)或[`Application.Styles`](http://reference.avaloniaui.net/api/Avalonia/Application/04017CAF)集合中。

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

样式还可以使用`StyleInclude`类从其他文件中引用，例如：

```markup
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Window.Styles>
        <StyleInclude Source="/CustomStyles.xaml" />
    </Window.Styles>

    <TextBlock Classes="h1">我是一个标题！</TextBlock>
</Window>
```

其中`CustomStyles.xaml`是一个具有根元素为`Style`或`Styles`的XAML文件，并作为应用程序中的资源进行引用，例如：

CustomStyles.xaml

```markup
<Styles xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Style Selector="TextBlock.h1">
        ...
    </Style>
</Styles>
```

请注意，与WPF/UWP不同，如果将样式添加到控件或应用程序的`ResourceDictionary`中，样式将不会生效。这是因为在Avalonia中，样式定义的顺序很重要，而`ResourceDictionary`是一个无序的字典结构。

### 样式类 <a href="#style-classes" id="style-classes"></a>

与CSS类似，控件可以被赋予\_样式类\_，这些样式类可以在选择器中使用。在XAML中，可以通过将`Classes`属性设置为一个以空格分隔的字符串列表来指定样式类。以下示例将`h1`和`blue`样式类应用于一个`Button`：

```markup
<Button Classes="h1 blue"/>
```

如果你需要根据条件添加或删除类，可以使用以下特殊语法：

```markup
<Button Classes.blue="{Binding IsSpecial}" />
```

样式类也可以在代码中使用`Classes`集合进行操作：

```csharp
control.Classes.Add("blue");
control.Classes.Remove("red");
```

### 伪类 <a href="#pseudoclasses" id="pseudoclasses"></a>

与CSS类似，控件可以具有伪类，这些是由控件本身定义而不是由用户定义的类。伪类以`:`字符开头。

一个伪类的例子是`:pointerover`（类似于CSS中的`:hover`）。

伪类提供了WPF中的`Triggers`和UWP中的`VisualStateManager`的功能：

```markup
<StackPanel>
  <StackPanel.Styles>
    <Style Selector="Border:pointerover">
      <Setter Property="Background" Value="Red"/>
    </Style>
  </StackPanel.Styles>
  <Border>
    <TextBlock>当悬停时，我将有红色背景。</TextBlock>
  </Border>
</StackPanel>
```

另一个例子涉及更改控件模板内部的属性：

```markup
<StackPanel>
  <StackPanel.Styles>
    <Style Selector="Button:pressed /template/ ContentPresenter">
        <Setter Property="TextBlock.Foreground" Value="Red"/>
    </Style>
  </StackPanel.Styles>
  <Button>当按下时，我将有红色文本。</Button>
</StackPanel>
```

其他伪类包括用于按钮的`:focus`、`:disabled`、`:pressed`，用于复选框的`:checked`等。

### 自定义伪类 <a href="#custom-pseudoclasses" id="custom-pseudoclasses"></a>

你可以为你的`CustomControl`或`TemplatedControl`创建自定义伪类。

下面的函数根据`StyledElement`上的布尔值添加或删除伪类：

```csharp
PseudoClasses.Set(":className", bool);
```

\*\*\*记住：\*_伪类总是以`:`开头！_

### 选择器 <a href="#selectors" id="selectors"></a>

\_选择器\_使用一种类似于CSS选择器的自定义语法来选择控件。以下是一些选择器的示例：

| 选择器                                  | 描述                                          |
| ------------------------------------ | ------------------------------------------- |
| `Button`                             | 选择所有 `Button` 控件                            |
| `Button.red`                         | 选择所有具有 `red` 样式类的 `Button` 控件               |
| `Button.red.large`                   | 选择所有具有 `red` 和 `large` 样式类的 `Button` 控件     |
| `Button:focus`                       | 选择所有具有 `:focus` 伪类的 `Button` 控件             |
| `Button.red:focus`                   | 选择所有具有 `red` 样式类和 `:focus` 伪类的 `Button` 控件  |
| `Button#myButton`                    | 选择名为 `myButton` 的 `Button` 控件               |
| `StackPanel Button.foo`              | 选择所有属于 `StackPanel` 子代的具有 `foo` 类的 `Button` |
| `StackPanel > Button.foo`            | 选择所有属于 `StackPanel` 子级的具有 `foo` 类的 `Button` |
| `Button /template/ ContentPresenter` | 选择在按钮模板中的所有 ContentPresenter 控件             |

更多信息请参阅选择器文档。

### 设置器 <a href="#setters" id="setters"></a>

样式的设置器描述了当选择器与控件匹配时会发生什么。它们是以属性/值对的形式表示的，格式如下：

```markup
<Setter Property="FontSize" Value="24"/>
<Setter Property="Padding" Value="4 2 0 4"/>
```

你还可以使用长格式语法来声明更复杂的对象值：

```markup
<Setter Property="MyProperty">
   <MyObject Property1="My Value"/>
</Setter>
```

可以使用设置器应用绑定，并可以绑定到目标控件的`DataContext`：

```markup
<Setter Property="FontSize" Value="{Binding SelectedFontSize}"/>
```

当样式与控件匹配时，所有的设置器都会应用到控件上。如果样式选择器导致样式不再与控件匹配，属性值将恢复为其下一个最高优先级的值。

注意，`Setter`创建了一个`Value`的单个实例，该实例将应用于所有与样式匹配的控件：如果对象是可变的，则更改将在所有控件上反映出来。因此，在设置器`Value`中的对象上的任何绑定都无法访问目标控件的`DataContext`，因为可能存在多个目标控件：

```xaml
<Style Selector="local|MyControl">
  <Setter Property="MyProperty">
     <MyObject Property1="{Binding MyViewModelProperty}"/>
  </Setter>
</Style>
```

在上面的示例中，绑定源将是`MyObject.DataContext`，而不是`MyControl.DataContext`，如果`MyObject`没有数据上下文，则绑定

将无法产生值。

注意：目前，如果你使用了编译绑定，你需要在`<Style>`元素中显式设置绑定源的数据类型：

```xaml
<Style Selector="MyControl" x:DataType="MyViewModelClass">
  <Setter Property="ControlProperty" Value="{Binding MyViewModelProperty}" />
</Style>
```

### 设置器中的模板 <a href="#templates-in-setters" id="templates-in-setters"></a>

如上所述，通常会创建一个设置器的`Value`的单个实例，并在所有匹配的控件之间共享。因此，要将控件用作设置器的值，必须将控件包装在`<Template>`中：

```xaml
<Style Selector="Border.empty">
  <Setter Property="Child">
    <Template>
      <TextBlock>No content available.</TextBlock>
    </Template>
  </Setter>
</Style>
```

### 样式优先级 <a href="#style-precedence" id="style-precedence"></a>

如果多个样式匹配一个控件，并且它们都试图设置相同的属性，则样式\_靠近控件的样式\_将获胜。考虑以下示例：

```xaml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Window.Styles>
        <Style Selector="TextBlock.h1">
            <Setter Property="FontSize" Value="24"/>
            <Setter Property="FontWeight" Value="Bold"/>
        </Style>
    </Window.Styles>

    <StackPanel>
        <StackPanel.Styles>
            <Style Selector="TextBlock.h1">
                <Setter Property="FontSize" Value="48"/>
                <Setter Property="Foreground" Value="Red"/>
            </Style>
        </StackPanel.Styles>

        <TextBlock Classes="h1">
            <StackPanel.Styles>
                <Style Selector="TextBlock.h1">
                    <Setter Property="Foreground" Value="Blue"/>
                </Style>
            </StackPanel.Styles>

            I'm a Heading!
        </TextBlock>
    </StackPanel>
</Window>
```

在这里，`h1`样式在多个位置定义。`TextBlock`最终将具有以下设置：

| 属性           | 值    | 来源           |
| ------------ | ---- | ------------ |
| `FontSize`   | 48   | `StackPanel` |
| `FontWeight` | Bold | `Window`     |
| `Foreground` | Blue | `TextBlock`  |

如果有多个样式设置器应用于一个属性，则具有优先权的值将是：

* 在控件最近的祖先中定义的样式的值
* 对于在同一个`Styles`集合中声明的两个样式，后面出现的样式
