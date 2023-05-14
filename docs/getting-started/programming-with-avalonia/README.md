# 使用Avalonia进行编程

Avalonia允许你使用[XAML标记语言](https://docs.avaloniaui.net/guides/basics/introduction-to-xaml)和C#（或其他.NET语言）编写应用程序。通常使用XAML标记语言实现应用程序的外观，使用代码实现其行为。

## 标记

XAML是一种基于XML的标记语言，它通过声明实现应用程序的外观。通常使用它创建窗口和用户控件，并在其中填充控件、形状和图形。

下面的示例使用XAML实现包含单个按钮的窗口的外观：

```markup
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        x:Class="AvaloniaApplication1.MainWindow"
        Title="Window with Button"
        Width="250" Height="100">

  <!-- Add button to window -->
  <Button Name="button">Click Me!</Button>

</Window>
```

具体来说，这个XAML定义了一个窗口和一个按钮，分别使用Window和Button元素。每个元素都配置了属性，例如Window元素的Title属性，用于指定窗口的标题栏文本。在运行时，Avalonia将在标记中定义的元素和属性转换为Avalonia类的实例。例如，Window元素将转换为Window类的实例，其Title属性是Title属性的值。

由于XAML是基于XML的，所以使用它组合的UI是通过嵌套元素的层次结构组装而成的，这被称为元素树。元素树提供了一种逻辑和直观的方式来创建和管理UI。

下面的图片显示了在Windows上运行以前面示例中定义的XAML为基础的用户界面：

## 代码后台

应用程序的主要行为是实现响应用户交互的功能，包括处理事件（例如单击菜单、工具栏或按钮）和调用业务逻辑和数据访问逻辑。

在Avalonia中，这种行为可以通过与标记相关联的代码实现。这种类型的代码称为[代码后台](https://docs.avaloniaui.net/guides/basics/code-behind)。

下面的示例显示了前面示例中更新后的标记和代码后台（请注意，有2个标签）：

{% tabs %}
{% tab title="XAML" %}
```markup
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        x:Class="AvaloniaApplication1.MainWindow"
        Title="Window with Button"
        Width="250" Height="100">

  <!-- Add button to window -->
  <Button Name="button" Click="button_Click">Click Me!</Button>

</Window>
```
{% endtab %}

{% tab title="C#" %}
```csharp
using Avalonia;
using Avalonia.Controls;
using Avalonia.Interactivity;
using Avalonia.Markup.Xaml;

namespace AvaloniaApplication1
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            // Generated with Avalonia.NameGenerator
            InitializeComponent();
        }

        public void button_Click(object sender, RoutedEventArgs e)
        {
            // Change button text when button is clicked.
            var button = (Button)sender;
            button.Content = "Hello, Avalonia!";
        }
    }
}
```
{% endtab %}
{% endtabs %}

在这个例子中，代码后台实现了一个从[`Window`](https://docs.avaloniaui.net/docs/getting-started/windows)类派生的类。`x:Class`属性用于将标记与代码后台类相关联。`InitializeComponent`从代码后台类的构造函数中调用，以将在标记中定义的UI与代码后台类合并。

你可以注意到`MainWindow`类被声明为`partial`，并且看不到`InitializeComponent`实现。这是因为[Avalonia.NameGenerator](https://github.com/AvaloniaUI/Avalonia.NameGenerator)包自动生成了`InitializeComponent`的部分类对应的主体代码，该包默认包含在Avalonia模板中。

`x:Class`和`InitializeComponent`的结合确保在创建实例时正确地初始化你的实现。代码后台类还为按钮的`Click`事件实现了一个事件处理程序。当单击按钮时，事件处理程序通过在`Button`控件上设置属性来更改按钮的文本。
