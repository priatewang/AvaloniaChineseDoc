# 故障排除

Avalonia的样式系统是XAML和CSS样式方法的结合，因此只了解其中一种技术的开发人员可能会对另一种技术的细节感到困惑。

让我们设想一个问题，即控件上的一个或多个样式设置器未应用。下面我们将列出常见的可能原因和解决方案。

### 选择器的目标控件不存在

Avalonia选择器（类似于CSS选择器）在没有任何匹配的控件时不会引发任何错误或警告。这包括使用不存在的名称或类，或者在没有子级可以匹配内部选择器时使用子选择器。原因很简单，一个样式可以针对许多在运行时创建或删除的控件，因此无法验证选择器。

### 目标属性被另一个样式覆盖

样式是按照声明的顺序应用的。如果有**多个**样式文件针对相同的控件属性，一个样式可以覆盖另一个样式：

{% code title="Styles2.axaml" %}
```markup
<Style Selector="TextBlock.header">
    <Style Property="Foreground" Value="Green" />
</Style>
```
{% endcode %}

{% code title="Styles1.axaml" %}
```markup
<Style Selector="TextBlock.header">
    <Style Property="Foreground" Value="Blue" />
    <Style Property="FontSize" Value="16" />
</Style>
```
{% endcode %}

{% code title="App.axaml" %}
```markup
<StyleInclude Source="Style1.axaml" />
<StyleInclude Source="Style2.axaml" />
```
{% endcode %}

这里首先应用了来自文件**Styles1.axaml**的样式，因此来自文件**Styles2.axaml**的样式中的设置器具有优先级。结果中的TextBlock将具有FontSize="16"和Foreground="Green"。在样式文件中也会发生相同的顺序优先级。

### 本地设置属性会覆盖样式

与WPF类似，Avalonia属性可以具有多个值，通常具有不同的优先级。

在此示例中，您可以看到本地值（直接在控件上定义）的优先级高于样式值，因此文本块将具有红色前景色：

```markup
<TextBlock Classes="header" Foreground="Red" />
...
<Style Selector="TextBlock.header">
    <Setter Property="Foreground" Value="Green" />
</Style>
```

您可以在[BindingPriority](http://reference.avaloniaui.net/api/Avalonia.Data/BindingPriority/)枚举中查看完整的值优先级列表，其中较低的枚举值具有较高的优先级。例如，`Animation`值具有最高的优先级，甚至可以覆盖本地值。

{% hint style="info" %}
一些默认的Avalonia样式在其模板中使用本地值而不是模

板绑定或样式设置器，这使得无法更新模板属性而不替换整个模板。
{% endhint %}

### 缺少样式伪类（触发器）选择器

假设您可能希望第二个样式覆盖前一个样式，但实际上没有覆盖的情况：

```markup
<Style Selector="Border:pointerover">
    <Setter Property="Background" Value="Blue" />
</Style>
<Style Selector="Border">
    <Setter Property="Background" Value="Red" />
</Style>
...
<Border Width="100" Height="100" Margin="100" />
```

在这个代码示例中，`Border`在正常情况下具有红色背景，当鼠标悬停在上面时具有蓝色背景。这是因为与CSS一样，更具体的选择器具有优先级。当您希望使用单个样式覆盖任何状态（pointerover、pressed或其他状态）的默认样式时，这是一个问题。为了实现这一点，您需要为这些状态创建新的样式。

{% hint style="info" %}
访问Avalonia源代码以查找[原始模板](https://github.com/AvaloniaUI/Avalonia/tree/master/src/Avalonia.Themes.Fluent/Controls)（当发生这种情况时）并将带有伪类的样式复制粘贴到您的代码中。
{% endhint %}

### 带有伪类的选择器无法覆盖默认样式

下面是一个可以预期在默认样式之上工作的样式代码示例：

```markup
<Style Selector="Button">
    <Setter Property="Background" Value="Red" />
</Style>
<Style Selector="Button:poinverover">
    <Setter Property="Background" Value="Blue" />
</Style>
```

您可能期望`Button`在默认情况下为红色，在鼠标悬停在上面时为蓝色。实际上，只有第一个样式的设置器将被应用，第二个样式将被忽略。

原因隐藏在Button的模板中。您可以在Avalonia源代码中找到默认模板（旧的[Default](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Themes.Default/Button.xaml)主题和新的[Fluent](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Themes.Fluent/Controls/Button.xaml)主题），但为了方便起见，这里我们简化了Fluent主题中的模板：

```markup
<Style Selector="Button">
    <Setter Property="Background" Value="{DynamicResource ButtonBackground}"/>
    <Setter Property="Template">
        <ControlTemplate>
            <ContentPresenter Name="PART_ContentPresenter"
                              Background="{TemplateBinding Background}"
                              Content="{TemplateBinding Content}"/>
        </ControlTemplate>
    </Setter>
</Style>
<Style Selector="Button:pointerover /template/ ContentPresenter#PART_ContentPresenter">
    <Setter Property="Background" Value="{DynamicResource ButtonBackgroundPointerOver}" />
</Style>
```

实际的背景是由`ContentPresenter`渲染的，在默认情况下，它绑定到按钮的`Background`属性。然而，在鼠标悬停状态下，选择器直接将背景应用于`ContentPresenter`（Button:pointerover /template/ ContentPresenter#PART\_ContentPresenter）。这就是为什么在前面的代码示例中我们的设置器被忽略的原因。更正后的代码也应该直接针对内容呈现器：

```markup
<!-- 这里 #PART_ContentPresenter 名称选择器不是必需的，但添加它可以获得更具体的样式 -->
<Style Selector="Button:pointerover /template/ ContentPresenter#PART_ContentPresenter">
    <Setter Property="Background" Value="Blue" />
</Style>
```

{% hint style="info" %}
您可以在默认主题（包括旧的Default和新的Fluent）中看到所有控件的这种行为，不仅限于Button。而且不仅限于Background，还包括其他与状态相关的属性。
{% endhint %}

{% hint style="info" %}
为什么默认样式直接更改ContentPresenter的Background属性而不是更改Button的Background属性？

这是因为如果用户在按钮上设置了本地值，它将覆盖所有样式，并使按钮始终具有相同的颜色。有关更多详细信息，请参阅此[reverted PR](https://github.com/AvaloniaUI/Avalonia/pull/2662#issuecomment-515764732)。
{% endhint %}

### 当样式不再应用时，无法恢复特定属性的先前值

在Avalonia中，我们有多种类型的属性，其中之一是Direct Property，根本不支持样式化。这些属性以简化的方式工作，以实现更低的开销和更高的性能，并且不存储根据优先级的多个值。相反，只保存最新的值，并且无法恢复。您可以在这里找到有关属性的更多详细信息。

典型的例子是CommandProperty。它被定义为DirectProperty，因此永远无法正常工作。在未来，尝试为直接属性设置样式将导致编译时错误，请参见[#6837](https://github.com/AvaloniaUI/Avalonia/issues/6837)。
