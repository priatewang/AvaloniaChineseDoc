# Model-View-ViewModel模式（MVVM）

除了在代码后台中编写代码外，Avalonia还支持使用Model-View-ViewModel（MVVM）模式。MVVM是一种常见的UI应用程序结构方式，它将视图逻辑与应用程序逻辑分离，以便使您的应用程序可以进行单元测试。

MVVM依赖于Avalonia的[绑定](https://docs.avaloniaui.net/docs/data-binding/bindings)功能，将您的应用程序分成视图层和ViewModel层。其中，视图层显示标准的Avalonia窗口和控件，ViewModel层定义应用程序的功能，独立于Avalonia本身。

以下示例显示了使用MVVM模式实现先前示例的代码：

{% tabs %}
{% tab title="XAML" %}
```markup
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        x:Class="AvaloniaApplication1.MainWindow"
        Title="Window with Button"
        Width="250" Height="100">

  <!-- Add button to window -->
  <Button Content="{Binding ButtonText}" Command="{Binding ButtonClicked}"/>

</Window>
```
{% endtab %}

{% tab title="C#" %}
```csharp
using System.ComponentModel;
using Avalonia.Controls;
using Avalonia.Markup.Xaml;

namespace AvaloniaApplication1
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
            DataContext = new MainWindowViewModel();
        }
    }

    public class MainWindowViewModel : INotifyPropertyChanged
    {
        string buttonText = "Click Me!";

        public string ButtonText
        {
            get => buttonText;
            set 
            {
                buttonText = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(ButtonText)));
            }
        }

        public event PropertyChangedEventHandler PropertyChanged;

        public void ButtonClicked() => ButtonText = "Hello, Avalonia!";
    }
}
```
{% endtab %}
{% endtabs %}

在此示例中，代码后台将`Window`的`DataContext`属性分配为`MainWindowViewModel`的实例。然后，XAML使用Avalonia [`{Binding}`](https://docs.avaloniaui.net/docs/data-binding/bindings)将`Button`的`Content`属性绑定到`MainWindowViewModel`上的`ButtonText`属性。它还将`Button`的[`Command`](https://docs.avaloniaui.net/docs/data-binding/binding-to-commands)属性绑定到`MainWindowViewModel`上的`ButtonClicked`方法。

当单击`Button`时，它会调用其`Command`，从而导致调用绑定的`MainWindowViewModel.ButtonClicked`方法。然后，该方法设置`ButtonText`属性，触发`INotifyPropertyChanged.PropertyChanged`事件，导致`Button`重新读取其绑定值并更新UI。
