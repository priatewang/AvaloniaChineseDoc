# 对齐方式、边距和内边距

Avalonia 控件提供了多种方式来定位元素。然而，实现理想的布局不仅仅是选择合适的 `Panel` 元素。对于元素的精确定位需要理解 `HorizontalAlignment`、`Margin`、`Padding` 和 `VerticalAlignment` 等属性。

下图展示了一个利用多个定位属性实现布局的场景。

这个示例中的 `Button` 元素看起来可能是随意放置的。然而，它们的位置实际上是通过组合边距、对齐和内边距来精确定位的。

下面的示例描述了如何创建上图中的布局。一个 `Border` 元素包含了一个父级 `StackPanel`，`Padding` 值为 15 个设备无关像素，用于包含子 `StackPanel` 周围狭窄的 `LightBlue` 带。`StackPanel` 的子元素用于演示本主题中详细介绍的各种定位属性。三个 `Button` 元素用于演示 `Margin` 和 `HorizontalAlignment` 属性。

```markup
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        x:Class="AvaloniaApplication2.MainWindow"
        Title="AvaloniaApplication2">
  <Border Background="LightBlue"
          BorderBrush="Black"
          BorderThickness="2"
          Padding="15">
    <StackPanel Background="White"
                HorizontalAlignment="Center"
                VerticalAlignment="Top">
      <TextBlock Margin="5,0"
                 FontSize="18"
                 HorizontalAlignment="Center">
        Alignment, Margin and Padding Sample
      </TextBlock>
      <Button HorizontalAlignment="Left" Margin="20">Button 1</Button>
      <Button HorizontalAlignment="Right" Margin="10">Button 2</Button>
      <Button HorizontalAlignment="Stretch">Button 3</Button>
    </StackPanel>
  </Border>
</Window>

```

下面的图示展示了上述示例中使用的各种定位属性的近距离视图。后续主题中的各节将详细介绍如何使用每个定位属性。

### 理解对齐属性

`HorizontalAlignment` 和 `VerticalAlignment` 属性描述了子元素在父元素分配的布局空间内应该如何定位。通过使用这些属性，可以精确定位子元素。例如，`DockPanel` 的子元素可以指定四种不同的水平对齐方式：`Left`，`Right`，`Center` 或者 `Stretch` 来填充可用空间。类似的值也可以用于垂直定位。

在元素上显式设置 `Height` 和 `Width` 属性优先于 `Stretch` 属性值。试图同时设置 `Height`、`Width` 和 `HorizontalAlignment` 值为 `Stretch` 将导致 `Stretch` 请求被忽略。

#### HorizontalAlignment 属性

`HorizontalAlignment` 属性声明应用于子元素的水平对齐特性。下表显示了 `HorizontalAlignment` 属性的每个可能的值。

| 成员              | 描述                                              |
| --------------- | ----------------------------------------------- |
| `Left`          | 子元素对齐于父元素分配的布局空间的左侧。                            |
| `Center`        | 子元素对齐于父元素分配的布局空间的中心。                            |
| `Right`         | 子元素对齐于父元素分配的布局空间的右侧。                            |
| `Stretch` (默认值) | 子元素被拉伸以填充父元素分配的布局空间。显式的 `Width` 和 `Height` 值优先。 |

以下示例演示如何将 `HorizontalAlignment` 属性应用于 `Button` 元素。为了更好地说明不同的渲染行为，每个属性值都显示在属性值中。

```markup
<Button HorizontalAlignment="Left">Button 1 (Left)</Button>
<Button HorizontalAlignment="Right">Button 2 (Right)</Button>
<Button HorizontalAlignment="Center">Button 3 (Center)</Button>
<Button HorizontalAlignment="Stretch">Button 4 (Stretch)</Button>
```

上面的代码生成一个类似下面图片的布局。每个 `HorizontalAlignment` 值的定位效果在图示中可见。

#### VerticalAlignment 属性

`VerticalAlignment` 属性描述应用于子元素的垂直对齐特性。下表显示了 `VerticalAlignment` 属性的每个可能的值。

| 成员              | 描述                                              |
| --------------- | ----------------------------------------------- |
| `Top`           | 子元素对齐于父元素分配的布局空间的顶部。                            |
| `Center`        | 子元素对齐于父元素分配的布局空间的中心。                            |
| `Bottom`        | 子元素对齐于父元素分配的布局空间的底部。                            |
| `Stretch` (默认值) | 子元素被拉伸以填充父元素分配的布局空间。显式的 `Width` 和 `Height` 值优先。 |

下面的示例展示了如何将 `VerticalAlignment` 属性应用于 `Button` 元素。每个属性值都被展示出来，以更好地说明各种渲染行为。为了展示每个属性值的布局行为，一个带有可见网格线的 `Grid` 元素被用作父元素。

```markup
<Border Background="LightBlue" BorderBrush="Black" BorderThickness="2" Padding="15">
    <Grid Background="White" ShowGridLines="True">
      <Grid.RowDefinitions>
        <RowDefinition Height="25"/>
        <RowDefinition Height="50"/>
        <RowDefinition Height="50"/>
        <RowDefinition Height="50"/>
        <RowDefinition Height="50"/>
      </Grid.RowDefinitions>
      <TextBlock Grid.Row="0" Grid.Column="0"
                 FontSize="18"
                 HorizontalAlignment="Center">
        VerticalAlignment Sample
      </TextBlock>
      <Button Grid.Row="1" Grid.Column="0" VerticalAlignment="Top">Button 1 (Top)</Button>
      <Button Grid.Row="2" Grid.Column="0" VerticalAlignment="Bottom">Button 2 (Bottom)</Button>
      <Button Grid.Row="3" Grid.Column="0" VerticalAlignment="Center">Button 3 (Center)</Button>
      <Button Grid.Row="4" Grid.Column="0" VerticalAlignment="Stretch">Button 4 (Stretch)</Button>
    </Grid>
</Border>
```

上面的代码生成了一个类似于下面的图片的布局。每个 `VerticalAlignment` 值的定位效果在图片中都可以看到。

### 理解 Margin 属性

`Margin` 属性描述了一个元素和它的子元素或同级元素之间的距离。`Margin` 值可以是统一的，可以使用类似 `Margin="20"` 的语法。这种语法将会向元素应用一个统一的距离，距离为 20 个设备独立像素。`Margin` 值也可以采用四个不同的值的形式，每个值描述了要应用到左边、顶部、右边和底部（按照这个顺序）的不同边距，例如 `Margin="0,10,5,25"`。正确使用 `Margin` 属性可以非常细致地控制元素的渲染位置和其相邻元素和子元素的渲染位置。

非零的 `Margin` 会在元素的 `Bounds` 之外应用空间。

下面的示例演示了如何在一组 `Button` 元素周围应用统一的边距。`Button` 元素被均匀地间隔，每个方向都有一个 10 像素的边距缓冲区。

```markup
<Button Margin="10">Button 7</Button>
<Button Margin="10">Button 8</Button>
<Button Margin="10">Button 9</Button>
```

在许多情况下，统一的边距不是合适的。在这种情况下，可以应用不均匀的间距。下面的示例演示了如何向子元素应用不均匀的边距间距。这些边距的顺序为：左、上、右、下。

```markup
<Button Margin="0,10,0,10">Button 1</Button>
<Button Margin="0,10,0,10">Button 2</Button>
<Button Margin="0,10,0,10">Button 3</Button>
```

#### 理解 Padding 属性

在大多数方面，`Padding` 与 `Margin` 类似。`Padding` 属性仅在一些类上公开，主要是为了方便：`Border`、`TemplatedControl` 和 `TextBlock` 是公开 `Padding` 属性的示例类。`Padding` 属性通过指定 `Thickness` 值来扩大子元素的有效大小。

下面的示例演示了如何向父 `Border` 元素应用 `Padding`。

```markup
<Border Background="LightBlue"
        BorderBrush="Black"
        BorderThickness="2"
        CornerRadius="45"
        Padding="25">
```

#### 在应用程序中使用对齐、边距和填充

`HorizontalAlignment`、`Margin`、`Padding`和`VerticalAlignment`提供了创建复杂UI所需的定位控制。您可以使用每个属性的效果来改变子元素的定位，从而实现创建动态应用程序和用户体验的灵活性。

下面的示例演示了本主题中详细介绍的每个概念。在本主题第一个示例中找到的基础设施上建立，该示例将`Grid`元素作为第一个示例中的`Border`的子元素。`Padding`应用于父`Border`元素。`Grid`用于将空间分割为三个子`StackPanel`元素。`Button`元素再次用于显示`Margin`和`HorizontalAlignment`的各种效果。每个`ColumnDefinition`中添加了`TextBlock`元素，以更好地定义应用于每个列中的`Button`元素的各种属性。

```markup
<Border Background="LightBlue"
        BorderBrush="Black"
        BorderThickness="2"
        CornerRadius="45"
        Padding="25">
    <Grid Background="White" ShowGridLines="True">
      <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto"/>
        <ColumnDefinition Width="*"/>
        <ColumnDefinition Width="Auto"/>
      </Grid.ColumnDefinitions>

    <StackPanel Grid.Column="0" Grid.Row="0"
                HorizontalAlignment="Left"
                Name="StackPanel1"
                VerticalAlignment="Top">
        <TextBlock FontSize="18" HorizontalAlignment="Center" Margin="0,0,0,15">StackPanel1</TextBlock>
        <Button Margin="0,10,0,10">Button 1</Button>
        <Button Margin="0,10,0,10">Button 2</Button>
        <Button Margin="0,10,0,10">Button 3</Button>
        <TextBlock>ColumnDefinition.Width="Auto"</TextBlock>
        <TextBlock>StackPanel.HorizontalAlignment="Left"</TextBlock>
        <TextBlock>StackPanel.VerticalAlignment="Top"</TextBlock>
        <TextBlock>StackPanel.Orientation="Vertical"</TextBlock>
        <TextBlock>Button.Margin="0,10,0,10"</TextBlock>
    </StackPanel>

    <StackPanel Grid.Column="1" Grid.Row="0"
                HorizontalAlignment="Stretch"
                Name="StackPanel2"
                VerticalAlignment="Top"
                Orientation="Vertical">
        <TextBlock FontSize="18" HorizontalAlignment="Center" Margin="0,0,0,15">StackPanel2</TextBlock>
        <Button Margin="10,0,10,0">Button 4</Button>
        <Button Margin="10,0,10,0">Button 5</Button>
        <Button Margin="10,0,10,0">Button 6</Button>
        <TextBlock HorizontalAlignment="Center">ColumnDefinition.Width="*"</TextBlock>
        <TextBlock HorizontalAlignment="Center">StackPanel.HorizontalAlignment="Stretch"</TextBlock>
        <TextBlock HorizontalAlignment="Center">StackPanel.VerticalAlignment="Top"</TextBlock>
        <TextBlock HorizontalAlignment="Center">StackPanel.Orientation="Horizontal"</TextBlock>
        <TextBlock HorizontalAlignment="Center">Button.Margin="10,0,10,0"</TextBlock>
    </StackPanel>

    <StackPanel Grid.Column="2" Grid.Row="0"
                HorizontalAlignment="Left"
                Name="StackPanel3"
                VerticalAlignment="Top">
        <TextBlock FontSize="18" HorizontalAlignment="Center" Margin="0,0,0,15">StackPanel3</TextBlock>
        <Button Margin="10">Button 7</Button>
        <Button Margin="10">Button 8</Button>
        <Button Margin="10">Button 9</Button>
        <TextBlock>ColumnDefinition.Width="Auto"</TextBlock>
        <TextBlock>StackPanel.HorizontalAlignment="Left"</TextBlock>
        <TextBlock>StackPanel.VerticalAlignment="Top"</TextBlock>
        <TextBlock>StackPanel.Orientation="Vertical"</TextBlock>
        <TextBlock>Button.Margin="10"</TextBlock>
    </StackPanel>
  </Grid>
</Border>
```

编译后，前面的应用程序将生成如下图所示的UI。各种属性值的效果在元素之间的间距中是明显的，每列元素的重要属性值在`TextBlock`元素中显示。
