# 控件的类型

如果您想创建自己的控件，Avalonia 中有三种主要的控件类型。首先要做的是选择最适合您的用例的控件类型。

### 用户控件 <a href="#user-controls" id="user-controls"></a>

`UserControl` 是编写控件的最简单的方法。这种类型的控件最适合于特定于应用程序的“视图”或“页面”。`UserControl` 与创建窗口的方式相同：通过从模板创建新的 `UserControl` 并添加控件来编写它们。

### 模板化控件 <a href="#templated-controls" id="templated-controls"></a>

`TemplatedControl` 最适合用于可以在各种应用程序之间共享的通用控件。它们是 _lookless_（无外观）的控件，这意味着它们可以为不同的主题和应用程序重新设计样式。大多数 Avalonia 定义的标准控件属于此类别。

{% hint style="info" %}
在 WPF/UWP 中，您将从 Control 类继承以创建新的模板化控件，但在 Avalonia 中，您应该从 `TemplatedControl` 继承。
{% endhint %}

{% hint style="info" %}
如果您想为 `TemplatedControl` 提供单独的 `Style` 文件，请记得通过 [`StyleInclude`](https://docs.avaloniaui.net/docs/styling/styles) 将此文件包含在应用程序中。
{% endhint %}

### 基本控件 <a href="#basic-control" id="basic-control"></a>

基本的 `Control` 是用户界面的基础 - 它们通过覆盖 `Visual.Render` 方法使用几何图形进行绘制。像 `TextBlock` 和 `Image` 这样的控件属于此类别。

{% hint style="info" %}
在 WPF/UWP 中，您将从 FrameworkElement 类继承以创建新的基本控件，但在 Avalonia 中，您应该从 `Control` 继承。
{% endhint %}
