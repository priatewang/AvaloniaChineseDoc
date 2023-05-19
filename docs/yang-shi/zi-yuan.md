# 资源

### 资源

通常，样式和控件需要共享资源，例如刷子和颜色（不仅限于此）。您可以将这些资源放在每个样式和控件上都可用的`Resources`字典中，然后在其他位置引用这些资源。

### 声明资源 <a href="#declaring-resources" id="declaring-resources"></a>

如果一个资源要在整个应用程序中可用，您可以在`App.xaml`/`App.axaml`中定义它：

```markup
<Application xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="MyApp.App">
  <Application.Resources>
    <SolidColorBrush x:Key="Warning">Yellow</SolidColorBrush>
  </Application.Resources>
</Application>
```

或者，您可以在`Window`或`UserControl`上声明资源：该资源将对`Window`/`UserControl`及其子控件可用：

```markup
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="MyApp.MyUserControl">
  <UserControl.Resources>
    <SolidColorBrush x:Key="Warning">Yellow</SolidColorBrush>
  </UserControl.Resources>
</UserControl>
```

实际上，您可以在任何控件上声明资源：

```markup
<Window xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="MyApp.MainWindow">
  <StackPanel>
    <StackPanel.Resources>
      <SolidColorBrush x:Key="Warning">Yellow</SolidColorBrush>
    </StackPanel.Resources>
  </StackPanel>
</Window>
```

您还可以在样式上声明资源：

```markup
<Style Selector="TextBlock.warn">
  <Style.Resources>
    <SolidColorBrush x:Key="Warning">Yellow</SolidColorBrush>
  </Style.Resources>
</Style>
```

### 引用资源 <a href="#referencing-resources" id="referencing-resources"></a>

您可以使用`{DynamicResource}`标记扩展来引用控件中的资源，例如：

```markup
<Border Background="{DynamicResource Warning}">
  Look out!
</Border>
```

或者，您可以使用`StaticResource`标记扩展来引用资源，它与`DynamicResource`相比有一些限制：

* 它不会响应资源的更改
* 资源需要在同一XAML文件中声明

作为回报，`StaticResource`不需要添加事件处理程序来监听资源的更改，这意味着它使用的内存稍微少一些。

### 覆盖资源 <a href="#overriding-resources" id="overriding-resources"></a>

资源是通过从`DynamicResource`或`StaticResource`的点开始，沿逻辑树向上查找，直到找到具有所请求的键的资源为止。这意味着资源可以在应用程序的子树中“覆盖”，例如：

```xaml
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="MyApp.MyUserControl">
  <UserControl.Resources>
    <SolidColorBrush x:Key="Warning">Yellow</SolidColorBrush>
  </UserControl.Resources>

  <StackPanel>
    <StackPanel.Resources>
      <SolidColorBrush x:Key="Warning">Orange</SolidColorBrush>
    </StackPanel.Resources>

    <Border Background="{DynamicResource Warning}">
      Look out!
    </Border>
  </StackPanel>
</UserControl>
```

在上述示例中，`Border`的背景将是`Orange`，因为其父级`StackPanel`已经“覆盖”了`UserControl`中声明的`Warning`资源。

### 合并的资源字典 <a href="#merged-resource-dictionaries" id="merged-resource-dictionaries"></a>

每个控件和样式上的`Resources`属性的类型为`ResourceDictionary`。资源字典还可以通过使用`MergedDictionaries`属性包含其他资源字典。要在另一个资源字典中包含一个资源字典，您可以使用`ResourceInclude`类，例如：

```xaml
<Window.Resources>
  <ResourceDictionary>
    <ResourceDictionary.MergedDictionaries>
      <ResourceInclude Source='/AnotherResourceDictionary.xaml'/>
    </ResourceDictionary.MergedDictionaries>
    <SolidColorBrush x:Key="Warning">Yellow</SolidColorBrush>
  </ResourceDictionary>
</Window.Resources>
```

其中，`AnotherResourceDictionary`是一个具有`ResourceDictionary`根的XAML文件，并作为应用程序中的一个资源进行包含。

### 资源解析 <a href="#resource-resolution" id="resource-resolution"></a>

如上所述，资源是通过从`DynamicResource`或`StaticResource`的点开始，沿逻辑树向上查找，直到找到具有所请求的键的资源为止。但是，样式和合并字典的存在使这个过程有些复杂。优先级如下：

* 控件资源
  * 合并字典
* 样式资源
  * 合并字典

对于下面的示例应用程序，对于在`Border`控件上定义的资源，资源查找将按照`[]`括号中指示的顺序进行：

```
Application
 |- Resources [11]
     |- Merged dictionary [12]
     |- Merged dictionary [13]
 |- Styles
     |- Resources [14]
         |- Merged dictionary [15]
         |- Merged dictionary [16]

Window
 |- Resources [6]
     |- Merged dictionary [7]
 |- Styles
     |- Resources [8]
         |- Merged dictionary [9]
         |- Merged dictionary [10]
 |- StackPanel
     |- Resources [1]
         |- Merged dictionary [2]
         |- Merged dictionary [3]
     |- Styles
         |- Resources [4]
             |- Merged dictionary [5]
     |- Border
```

### 主题资源 <a href="#theme-resources" id="theme-resources"></a>

主题通常将刷子、颜色和其他设置定义为资源。通过更改这些资源，您可以从深色主题切换到浅色主题。定义的资源通常是针对所使用的主题的，但是您可以在默认主题中查看定义的资源，[点击此处](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Themes.Default/Accents/BaseLight.xaml)。
