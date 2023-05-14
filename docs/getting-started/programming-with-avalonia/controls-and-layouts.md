# 控件和布局

### 控件 <a href="#controls" id="controls"></a>

Avalonia 提供了许多核心控件，以下是一些常见的控件：

* 按钮：`Button`、`RepeatButton`
* 数据显示：`ItemsControl`、`ItemsRepeater`、`ListBox`、`TreeView`
* 输入：`CheckBox`、`ComboBox`、`RadioButton`、`Slider`、`TextBox`
* 布局：`Border`、`Canvas`、`DockPanel`、`Expander`、`Grid`、`GridSplitter`、`Panel`、`Separator`、`ScrollBar`、`ScrollViewer`、`StackPanel`、`Viewbox`、`WrapPanel`
* 菜单：`ContextMenu`、`Menu`、`NativeMenu`
* 导航：`TabControl`、`TabStrip`
* 用户信息：`ProgressBar`、`TextBlock`、`ToolTip`

## 输入和命令

控件最常检测和响应用户输入。Avalonia 输入系统使用直接事件和路由事件来支持文本输入、焦点管理和鼠标定位。

应用程序通常具有复杂的输入要求。Avalonia 提供了一个命令系统，将用户输入动作与响应这些动作的代码分离。

### 布局 <a href="#layout" id="layout"></a>

当您创建用户界面时，需要按位置和大小排列控件以形成布局。任何布局的关键要求是适应窗口大小和显示设置的变化。Avalonia提供了一个一流的、可扩展的布局系统来避免您编写适应布局的代码。

布局系统的基石是相对定位，它增加了适应变化的窗口和显示条件的能力。此外，布局系统管理控件之间的协商以确定布局。该协商是一个两步过程：首先，控件告诉其父控件它需要什么位置和大小；其次，父控件告诉控件它可以使用什么空间。

布局系统通过基本的Avalonia类暴露给子控件。对于常见的布局，如网格、堆叠和停靠，Avalonia包括了几个布局控件：

* [`Panel`](https://docs.avaloniaui.net/docs/controls/panel)：子控件重叠在一起以填充面板
* [`DockPanel`](https://docs.avaloniaui.net/docs/controls/dockpanel)：子控件对齐到面板的边缘
* [`StackPanel`](https://docs.avaloniaui.net/docs/controls/stackpanel)：子控件垂直或水平堆叠
* [`WrapPanel`](https://docs.avaloniaui.net/docs/controls/wrappanel)：子控件按从左到右的顺序定位，并在当前行的控件数量超过空间允许时换行到下一行
* [`Grid`](https://docs.avaloniaui.net/docs/controls/grid)：子控件按行和列定位
* [`Canvas`](https://docs.avaloniaui.net/docs/controls/canvas)：子控件提供其自己的布局

您还可以通过从[`Panel`](https://docs.avaloniaui.net/docs/controls/panel)类派生来创建自己的布局。
