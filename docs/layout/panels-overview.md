# 面板概述

`Panel` 元素是控制元素渲染的组件 - 其大小和尺寸，它们的位置以及其子内容的排列。Avalonia 提供了许多预定义的 `Panel` 元素以及构建自定义 `Panel` 元素的能力。

## Panel 类 <a href="#the-panel-class" id="the-panel-class"></a>

`Panel` 是 Avalonia 中提供布局支持的所有元素的基类。派生的 `Panel` 元素用于在 XAML 和代码中定位和排列元素。

Avalonia 包括一套全面的派生面板实现，使得许多复杂的布局成为可能。这些派生类公开了属性和方法，使得大多数标准 UI 场景成为可能。无法找到满足其需求的子元素排列行为的开发人员可以通过重写 `ArrangeOverride` 和 `MeasureOverride` 方法来创建新的布局。有关自定义布局行为的更多信息，请参见 创建自定义面板。

### 面板公共成员 <a href="#panel-common-members" id="panel-common-members"></a>

所有 `Panel` 元素都支持 `Control` 定义的基本大小和定位属性，包括 `Height`、`Width`、`HorizontalAlignment`、`VerticalAlignment` 和 `Margin`。有关 `Control` 定义的定位属性的其他信息，请参见 对齐、边距和填充概述。

`Panel` 公开了其他一些属性，在理解和使用布局时非常重要。`Background` 属性用于用 `Brush` 填充派生面板元素边界之间的区域。`Children` 表示 `Panel` 由哪些子元素组成的集合。

**附加属性**

派生面板元素广泛使用附加属性。附加属性是一种特殊形式的依赖属性，不具有常规的公共语言运行时（CLR）属性“包装程序”。附加属性在 XAML 中有一种专门的语法，可以在以下示例中看到。

附加属性的一个目的是允许子元素存储由父元素实际定义的属性的唯一值。此功能的一个应用是使子元素通知父元素它们希望在 UI 中如何呈现，这对于应用程序布局非常有用。

### 用户界面面板 <a href="#user-interface-panels" id="user-interface-panels"></a>

在 Avalonia 中有几个面板类专门针对 UI 场景进行了优化：`Panel`、`Canvas`、`DockPanel`、`Grid`、`StackPanel`、`WrapPanel` 和 `RelativePanel`。这些面板元素易于使用，功能丰富，对于大多数应用程序来说足够灵活和可扩展。

**Canvas**

`Canvas` 元素允许根据绝对 _x_ 和 _y_ 坐标定位内容。元素可以在独特位置上绘制；或者，如果元素占用相同的坐标，则它们在标记中出现的顺序确定它们绘制的顺序。

`Canvas` 提供了任何 `Panel` 中最灵活的布局支持。`Height` 和 `Width` 属性用于定义 `Canvas` 的区域，其中内部的元素被赋予相对于父 `Canvas` 区域的绝对坐标。四个附加属性，`Canvas.Left`、`Canvas.Top`、`Canvas.Right` 和 `Canvas.Bottom`，允许在 `Canvas` 中精细控制对象的位置，使开发人员可以精确地在屏幕上定位和排列元素。

**Canvas 中的 ClipToBounds**

`Canvas` 可以将子元素定位在屏幕上的任何位置，甚至可以在其定义的 `Height` 和 `Width` 之外的坐标上。此外，`Canvas` 不受其子元素的大小影响。因此，子元素可能会在父 `Canvas` 的边界矩形之外超出其他元素。`Canvas` 的默认行为是允许子元素绘制超出父 `Canvas` 的边界。如果不希望出现这种行为，则可以将 `ClipToBounds` 属性设置为 `true`。这会使 `Canvas` 剪切到其自身的大小。`Canvas` 是唯一允许子元素在其边界之外绘制的布局元素。

**定义和使用 Canvas**

可以通过 XAML 或代码简单地实例化 `Canvas`。以下示例演示如何使用 `Canvas` 绝对定位内容。这段代码生成了三个 100 像素正方形。第一个正方形是红色的，它的左上角 (_x, y_) 位置指定为 (0, 0)。第二个正方形是绿色的，它的左上角位置是 (100, 100)，刚好在第一个正方形的下方和右侧。第三个正方形是蓝色的，它的左上角位置是 (50, 50)，因此包含了第一个正方形的右下象限和第二个正方形的左上象限。因为第三个正方形是最后一个被布局的，所以它看起来在其他两个正方形的上面——也就是说，重叠的部分采用第三个正方形的颜色。

C#

```csharp
// Create the Canvas
myParentCanvas = new Canvas();
myParentCanvas.Width = 400;
myParentCanvas.Height = 400;

// Define child Canvas elements
myCanvas1 = new Canvas();
myCanvas1.Background = Brushes.Red;
myCanvas1.Height = 100;
myCanvas1.Width = 100;
Canvas.SetTop(myCanvas1, 0);
Canvas.SetLeft(myCanvas1, 0);

myCanvas2 = new Canvas();
myCanvas2.Background = Brushes.Green;
myCanvas2.Height = 100;
myCanvas2.Width = 100;
Canvas.SetTop(myCanvas2, 100);
Canvas.SetLeft(myCanvas2, 100);

myCanvas3 = new Canvas();
myCanvas3.Background = Brushes.Blue;
myCanvas3.Height = 100;
myCanvas3.Width = 100;
Canvas.SetTop(myCanvas3, 50);
Canvas.SetLeft(myCanvas3, 50);

// Add child elements to the Canvas' Children collection
myParentCanvas.Children.Add(myCanvas1);
myParentCanvas.Children.Add(myCanvas2);
myParentCanvas.Children.Add(myCanvas3);
```

XAML

```xaml
<Canvas Height="400" Width="400">
  <Canvas Height="100" Width="100" Top="0" Left="0" Background="Red"/>
  <Canvas Height="100" Width="100" Top="100" Left="100" Background="Green"/>
  <Canvas Height="100" Width="100" Top="50" Left="50" Background="Blue"/>
</Canvas>
```

**DockPanel（停靠面板）**

`DockPanel` 元素使用子内容元素中设置的 `DockPanel.Dock` 附加属性来沿容器的边缘定位内容。当 `DockPanel.Dock` 设置为 `Top` 或 `Bottom` 时，它会将子元素放置在彼此的上方或下方。当 `DockPanel.Dock` 设置为 `Left` 或 `Right` 时，它会将子元素放置在彼此的左侧或右侧。`LastChildFill` 属性确定了在 `DockPanel` 的最后一个添加的子元素的位置。

您可以使用 `DockPanel` 来定位一组相关的控件，例如一组按钮。或者，您可以使用它来创建一个 "分隔窗格" 用户界面。

**按内容大小调整大小**

如果未指定其 `Height` 和 `Width` 属性，`DockPanel` 将根据其内容大小进行调整。大小可以增加或减小以适应其子元素的大小。但是，当指定了这些属性并且没有足够的空间容纳下一个指定的子元素时，`DockPanel` 不会显示该子元素或后续子元素，并且不会测量后续子元素。

**LastChildFill**

默认情况下，`DockPanel` 元素的最后一个子元素将"填充"剩余未分配的空间。如果不希望出现这种行为，将 `LastChildFill` 属性设置为 `false`。

**定义和使用 DockPanel**

以下示例演示如何使用 `DockPanel` 分割空间。五个 `Border` 元素作为父 `DockPanel` 的子级添加。每个元素使用 `DockPanel` 的不同定位属性来分隔空间。最后一个元素 "填充" 剩余未分配的空间。

C#

```csharp
// Create the DockPanel
DockPanel myDockPanel = new DockPanel();
myDockPanel.LastChildFill = true;

// Define the child content
Border myBorder1 = new Border();
myBorder1.Height = 25;
myBorder1.Background = Brushes.SkyBlue;
myBorder1.BorderBrush = Brushes.Black;
myBorder1.BorderThickness = new Thickness(1);
DockPanel.SetDock(myBorder1, Dock.Top);
TextBlock myTextBlock1 = new TextBlock();
myTextBlock1.Foreground = Brushes.Black;
myTextBlock1.Text = "Dock = Top";
myBorder1.Child = myTextBlock1;

Border myBorder2 = new Border();
myBorder2.Height = 25;
myBorder2.Background = Brushes.SkyBlue;
myBorder2.BorderBrush = Brushes.Black;
myBorder2.BorderThickness = new Thickness(1);
DockPanel.SetDock(myBorder2, Dock.Top);
TextBlock myTextBlock2 = new TextBlock();
myTextBlock2.Foreground = Brushes.Black;
myTextBlock2.Text = "Dock = Top";
myBorder2.Child = myTextBlock2;

Border myBorder3 = new Border();
myBorder3.Height = 25;
myBorder3.Background = Brushes.LemonChiffon;
myBorder3.BorderBrush = Brushes.Black;
myBorder3.BorderThickness = new Thickness(1);
DockPanel.SetDock(myBorder3, Dock.Bottom);
TextBlock myTextBlock3 = new TextBlock();
myTextBlock3.Foreground = Brushes.Black;
myTextBlock3.Text = "Dock = Bottom";
myBorder3.Child = myTextBlock3;

Border myBorder4 = new Border();
myBorder4.Width = 200;
myBorder4.Background = Brushes.PaleGreen;
myBorder4.BorderBrush = Brushes.Black;
myBorder4.BorderThickness = new Thickness(1);
DockPanel.SetDock(myBorder4, Dock.Left);
TextBlock myTextBlock4 = new TextBlock();
myTextBlock4.Foreground = Brushes.Black;
myTextBlock4.Text = "Dock = Left";
myBorder4.Child = myTextBlock4;

Border myBorder5 = new Border();
myBorder5.Background = Brushes.White;
myBorder5.BorderBrush = Brushes.Black;
myBorder5.BorderThickness = new Thickness(1);
TextBlock myTextBlock5 = new TextBlock();
myTextBlock5.Foreground = Brushes.Black;
myTextBlock5.Text = "This content will Fill the remaining space";
myBorder5.Child = myTextBlock5;

// Add child elements to the DockPanel Children collection
myDockPanel.Children.Add(myBorder1);
myDockPanel.Children.Add(myBorder2);
myDockPanel.Children.Add(myBorder3);
myDockPanel.Children.Add(myBorder4);
myDockPanel.Children.Add(myBorder5);
```

XAML

```markup
<DockPanel LastChildFill="True">
  <Border Height="25" Background="SkyBlue" BorderBrush="Black" BorderThickness="1" DockPanel.Dock="Top">
    <TextBlock Foreground="Black">Dock = "Top"</TextBlock>
  </Border>
  <Border Height="25" Background="SkyBlue" BorderBrush="Black" BorderThickness="1" DockPanel.Dock="Top">
    <TextBlock Foreground="Black">Dock = "Top"</TextBlock>
  </Border>
  <Border Height="25" Background="LemonChiffon" BorderBrush="Black" BorderThickness="1" DockPanel.Dock="Bottom">
    <TextBlock Foreground="Black">Dock = "Bottom"</TextBlock>
  </Border>
  <Border Width="200" Background="PaleGreen" BorderBrush="Black" BorderThickness="1" DockPanel.Dock="Left">
    <TextBlock Foreground="Black">Dock = "Left"</TextBlock>
  </Border>
  <Border Background="White" BorderBrush="Black" BorderThickness="1">
    <TextBlock Foreground="Black">This content will "Fill" the remaining space</TextBlock>
  </Border>
</DockPanel>
```

**网格（Grid）**

`Grid`元素将绝对定位和表格数据控件的功能合并在一起，允许您轻松地定位和设置元素。 `Grid`允许您定义灵活的行和列分组，并提供了在多个`Grid`元素之间共享大小信息的机制。

**列和行的尺寸行为**

在`Grid`内定义的列和行可以利用`Star`大小来按比例分配剩余空间。当行或列的高度或宽度被设置为`Star`时，该列或行将按比例获得剩余的可用空间。这与`Auto`不同，后者将根据列或行中内容的大小均匀分配空间。在使用XAML时，这个值用`*`或`2*`来表示。在第一种情况下，行或列将获得一倍的可用空间，在第二种情况下，将获得两倍的空间，以此类推。通过将此技术与`HorizontalAlignment`和`VerticalAlignment`值设置为`Stretch`相结合，可以按百分比将布局空间分配为屏幕空间。`Grid`是唯一可以以这种方式分配空间的布局面板。

**定义和使用网格**

以下示例演示了如何构建类似于Windows开始菜单上的运行对话框中发现的UI。

C#

```csharp
// Create the Grid.
grid1 = new Grid ();
grid1.Background = Brushes.Gainsboro;
grid1.HorizontalAlignment = HorizontalAlignment.Left;
grid1.VerticalAlignment = VerticalAlignment.Top;
grid1.ShowGridLines = true;
grid1.Width = 425;
grid1.Height = 165;

// Define the Columns.
colDef1 = new ColumnDefinition();
colDef1.Width = new GridLength(1, GridUnitType.Auto);
colDef2 = new ColumnDefinition();
colDef2.Width = new GridLength(1, GridUnitType.Star);
colDef3 = new ColumnDefinition();
colDef3.Width = new GridLength(1, GridUnitType.Star);
colDef4 = new ColumnDefinition();
colDef4.Width = new GridLength(1, GridUnitType.Star);
colDef5 = new ColumnDefinition();
colDef5.Width = new GridLength(1, GridUnitType.Star);
grid1.ColumnDefinitions.Add(colDef1);
grid1.ColumnDefinitions.Add(colDef2);
grid1.ColumnDefinitions.Add(colDef3);
grid1.ColumnDefinitions.Add(colDef4);
grid1.ColumnDefinitions.Add(colDef5);

// Define the Rows.
rowDef1 = new RowDefinition();
rowDef1.Height = new GridLength(1, GridUnitType.Auto);
rowDef2 = new RowDefinition();
rowDef2.Height = new GridLength(1, GridUnitType.Auto);
rowDef3 = new RowDefinition();
rowDef3.Height = new GridLength(1, GridUnitType.Star);
rowDef4 = new RowDefinition();
rowDef4.Height = new GridLength(1, GridUnitType.Auto);
grid1.RowDefinitions.Add(rowDef1);
grid1.RowDefinitions.Add(rowDef2);
grid1.RowDefinitions.Add(rowDef3);
grid1.RowDefinitions.Add(rowDef4);

// Add the Image.
img1 = new Image();
img1.Source = runicon;
Grid.SetRow(img1, 0);
Grid.SetColumn(img1, 0);

// Add the main application dialog.
txt1 = new TextBlock();
txt1.Text = "Type the name of a program, folder, document, or Internet resource, and Windows will open it for you.";
txt1.TextWrapping = TextWrapping.Wrap;
Grid.SetColumnSpan(txt1, 4);
Grid.SetRow(txt1, 0);
Grid.SetColumn(txt1, 1);

// Add the second text cell to the Grid.
txt2 = new TextBlock();
txt2.Text = "Open:";
Grid.SetRow(txt2, 1);
Grid.SetColumn(txt2, 0);

// Add the TextBox control.
tb1 = new TextBox();
Grid.SetRow(tb1, 1);
Grid.SetColumn(tb1, 1);
Grid.SetColumnSpan(tb1, 5);

// Add the buttons.
button1 = new Button();
button2 = new Button();
button3 = new Button();
button1.Content = "OK";
button2.Content = "Cancel";
button3.Content = "Browse ...";
Grid.SetRow(button1, 3);
Grid.SetColumn(button1, 2);
button1.Margin = new Thickness(10, 0, 10, 15);
button2.Margin = new Thickness(10, 0, 10, 15);
button3.Margin = new Thickness(10, 0, 10, 15);
Grid.SetRow(button2, 3);
Grid.SetColumn(button2, 3);
Grid.SetRow(button3, 3);
Grid.SetColumn(button3, 4);

grid1.Children.Add(img1);
grid1.Children.Add(txt1);
grid1.Children.Add(txt2);
grid1.Children.Add(tb1);
grid1.Children.Add(button1);
grid1.Children.Add(button2);
grid1.Children.Add(button3);
```

**StackPanel**

`StackPanel`（堆栈面板）可以沿着指定的方向"堆叠"元素。默认的堆叠方向是垂直的。可以使用`Orientation`属性来控制内容流向。

**StackPanel vs. DockPanel**

尽管`DockPanel`也可以"堆叠"子元素，但在某些用法场景下，`DockPanel`和`StackPanel`的结果并不相似。例如，在`DockPanel`中，子元素的顺序会影响它们的大小，但在`StackPanel`中却不会。这是因为`StackPanel`在堆叠方向上测量为`PositiveInfinity`，而`DockPanel`只测量可用的大小。

**定义和使用 StackPanel**

以下示例演示了如何使用`StackPanel`创建一组垂直排列的按钮。要进行水平定位，请将`Orientation`属性设置为`Horizontal`。

C#

```csharp
// Define the StackPanel
myStackPanel = new StackPanel();
myStackPanel.HorizontalAlignment = HorizontalAlignment.Left;
myStackPanel.VerticalAlignment = VerticalAlignment.Top;

// Define child content
Button myButton1 = new Button();
myButton1.Content = "Button 1";
Button myButton2 = new Button();
myButton2.Content = "Button 2";
Button myButton3 = new Button();
myButton3.Content = "Button 3";

// Add child elements to the parent StackPanel
myStackPanel.Children.Add(myButton1);
myStackPanel.Children.Add(myButton2);
myStackPanel.Children.Add(myButton3);
```

**WrapPanel**

`WrapPanel` 用于将子元素从左到右依次排列，并在达到父容器边缘时将内容换行。内容可以水平或垂直定向。`WrapPanel` 对于简单的流式 UI 场景非常有用。它还可以用于将统一的大小应用于其所有子元素。

下面的示例演示了如何创建一个 `WrapPanel` 来显示 `Button` 控件，当它们到达容器边缘时进行换行。

C#

```csharp
// 实例化一个新的 WrapPanel 并设置属性
myWrapPanel = new WrapPanel();
myWrapPanel.Background = System.Windows.Media.Brushes.Azure;
myWrapPanel.Orientation = Orientation.Horizontal;
myWrapPanel.Width = 200;
myWrapPanel.HorizontalAlignment = HorizontalAlignment.Left;
myWrapPanel.VerticalAlignment = VerticalAlignment.Top;

// 定义 3 个按钮元素。最后三个按钮的宽度为 75，因此第四个按钮换行到下一行。
btn1 = new Button();
btn1.Content = "Button 1";
btn1.Width = 200;
btn2 = new Button();
btn2.Content = "Button 2";
btn2.Width = 75;
btn3 = new Button();
btn3.Content = "Button 3";
btn3.Width = 75;
btn4 = new Button();
btn4.Content = "Button 4";
btn4.Width = 75;

// 使用 Children.Add 方法将按钮添加到父 WrapPanel 中。
myWrapPanel.Children.Add(btn1);
myWrapPanel.Children.Add(btn2);
myWrapPanel.Children.Add(btn3);
myWrapPanel.Children.Add(btn4);
```

XAML

```markup
<Border HorizontalAlignment="Left" VerticalAlignment="Top" BorderBrush="Black" BorderThickness="2">
  <WrapPanel Background="LightBlue" Width="200" Height="100">
    <Button Width="200">Button 1</Button>
    <Button>Button 2</Button>
    <Button>Button 3</Button>
    <Button>Button 4</Button>
  </WrapPanel>
</Border>
```

### 嵌套面板元素

`Panel` 元素可以嵌套在彼此中，以产生复杂的布局。这在某些情况下非常有用，其中一个 `Panel` 对于 UI 的一部分是理想的，但可能无法满足 UI 的不同部分的需求。

您的应用程序支持嵌套的数量没有实际限制，但通常最好限制您的应用程序仅使用实际需要的那些面板来实现所需的布局。在许多情况下，`Grid` 元素可以代替嵌套面板，因为它作为布局容器非常灵活。通过使不必要的元素离开树，这可以通过保持性能来提高应用程序的性能。
