# 📐 布局

## 面板 <a href="#panels" id="panels"></a>

Avalonia 包含了一组派生自 `Panel` 的元素。这些 `Panel` 元素使得许多复杂的布局变得容易实现。例如，可以使用 `StackPanel` 元素轻松地堆叠元素，而使用 `Canvas` 则可以实现更复杂、自由流动的布局。

以下表格总结了可用的 `Panel` 控件：

| 名称              | 描述                                                                 |
| --------------- | ------------------------------------------------------------------ |
| `Panel`         | 将所有子元素布局以填充 `Panel` 边界的区域。                                         |
| `Canvas`        | 定义一个区域，在此区域内可以通过相对于 `Canvas` 区域的坐标显式定位子元素。                         |
| `DockPanel`     | 定义一个区域，在此区域内可以相对于彼此按水平或垂直方向排列子元素。                                  |
| `Grid`          | 定义一个灵活的网格区域，由列和行组成。                                                |
| `RelativePanel` | 相对于其他元素或面板本身布置子元素。                                                 |
| `StackPanel`    | 将子元素布置为单行，可以水平或垂直方向定向。                                             |
| `WrapPanel`     | 将子元素按顺序从左到右排列，当内容超出容器框的边缘时，将其断开到下一行。随后的排序顺序按顺序从上到下或从右到左，取决于方向属性的值。 |

在 WPF 中，`Panel` 是一个抽象类，并且通常使用不带行/列的 `Grid` 布置多个控件以填充可用空间。在 Avalonia 中，`Panel` 是一个可用的控件，具有与不带行/列的 `Grid` 相同的布局行为，但运行时占用更少的资源。

## 元素边界框 <a href="#element-bounding-boxes" id="element-bounding-boxes"></a>

在 Avalonia 中考虑布局时，了解包围所有元素的边界框非常重要。每个被布局系统使用的 `Control` 可以看作是一个矩形，插入到布局中。`Bounds` 属性返回元素布局分配的边界。矩形的大小通过计算可用屏幕空间、约束的大小、布局特定的属性（如边距和填充）以及父级 `Panel` 元素的个体行为来确定。通过处理这些数据，布局系统能够计算出特定 `Panel` 的所有子元素的位置。需要记住的是，在父元素上定义的大小特征，例如 `Border`，会影响其子元素。

## 布局系统 <a href="#the-layout-system" id="the-layout-system"></a>

简单来说，布局是一个递归系统，它会导致元素被大小和位置确定并最终绘制。更具体地说，布局描述了对 `Panel` 元素的 `Children` 集合的成员进行测量和排列的过程。布局是一个非常耗费计算资源的过程。`Children` 集合越大，需要进行的计算次数就越多。同时，所拥有的 `Children` 集合的 `Panel` 元素所定义的布局行为也会引入复杂性。一个相对简单的 `Panel`，比如 `Canvas`，在性能上可以比一个更复杂的 `Panel`，比如 `Grid`，有明显的优势。

每当一个子控件改变其位置时，它都有可能触发布局系统的新一轮处理。因此，了解可以触发布局系统的事件非常重要，不必要的触发会导致应用程序的性能下降。以下是布局系统被调用时的过程描述。

1. 一个子 `UIElement` 开始布局过程，首先测量其核心属性。
2. 对 `Control` 上定义的大小属性进行评估，例如 `Width`、`Height` 和 `Margin`。
3. 应用 `Panel` 特定的逻辑，例如 `Dock` 方向或堆叠的 `Orientation`。
4. 所有子元素测量完毕后，排列内容。
5. 在屏幕上绘制 `Children` 集合。
6. 如果添加了额外的 `Children` 到集合中，则会再次调用该过程。

这个过程以及如何调用它将在下面的章节中详细介绍。

## 测量和排列子元素 <a href="#measuring-and-arranging-children" id="measuring-and-arranging-children"></a>

布局系统为`Children`集合的每个成员完成两次遍历，分别为测量遍历和排列遍历。每个子`Panel`提供其自己的`MeasureOverride`和`ArrangeOverride`方法来实现其自己特定的布局行为。

在测量遍历期间，对`Children`集合的每个成员进行评估。该过程始于对`Measure`方法的调用。该方法在父`Panel`元素的实现中被调用，无需显式调用即可进行布局。

首先，会评估`Visual`的本机大小属性，例如`Clip`和`IsVisible`。这会生成一个约束，该约束传递给`MeasureCore`。

然后，处理影响约束值的框架属性。这些属性通常描述底层`Control`的大小特性，例如其`Height`、`Width`和`Margin`。每个这些属性都可以更改显示元素所需的空间。然后，将约束作为参数调用`MeasureOverride`。

由于`Bounds`是一个计算出来的值，因此您应该意识到，由于布局系统的各种操作，可能会有多个或递增的报告更改。布局系统可能正在为子元素计算所需的度量空间，由父元素约束等。

测量遍历的最终目标是让子元素确定其`DesiredSize`，这是在`MeasureCore`调用期间发生的。`DesiredSize`值由`Measure`存储，供内容排列遍历使用。

排列遍历始于对`Arrange`方法的调用。在排列遍历期间，父`Panel`元素生成代表子元素边界的矩形。该值传递给`ArrangeCore`方法进行处理。

`ArrangeCore`方法评估子元素的`DesiredSize`，并评估任何可能影响元素呈现大小的附加边距。`ArrangeCore`生成一个排列大小，将其作为参数传递给`Panel`的`ArrangeOverride`方法。`ArrangeOverride`生成子元素的finalSize。最后，`ArrangeCore`方法对偏移属性（如边距和对齐方式）进行最终评估，并将子元素放置在其布局插槽内。子元素不必（并且通常不会）填充整个分配的空间。然后将控制权返回到父`Panel`，布局过程完成。

## 本节内容 <a href="#in-this-section" id="in-this-section"></a>

* [面板概述](panels-overview.md)
* [对齐、边距和内边距](alignment-margins-and-padding.md)
* [创建自定义面板](create-a-custom-panel.md)
