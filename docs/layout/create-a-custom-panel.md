# 创建自定义面板

这个例子展示了如何覆盖默认的 `Panel` 元素布局行为，创建从 `Panel` 派生的自定义布局元素。

该示例定义了一个简单的自定义 `Panel` 元素叫做 `PlotPanel`，它根据两个硬编码的 x 和 y 坐标来定位子元素。在这个示例中，`x` 和 `y` 都设置为 `50`，因此所有子元素都在 x 和 y 轴上的该位置上定位。

为了实现自定义的 `Panel` 行为，该示例使用了 `MeasureOverride` 和 `ArrangeOverride` 方法。每个方法返回必要的 `Size` 数据，以便定位和渲染子元素。

```csharp
public class PlotPanel : Panel
{
    // 覆盖 Panel 的默认 Measure 方法
    protected override Size MeasureOverride(Size availableSize)
    {
        var panelDesiredSize = new Size();

        // 在我们的例子中，我们只有一个子元素。
        // 报告我们的面板只需要其唯一子元素的大小。
        foreach (var child in Children)
        {
            child.Measure(availableSize);
            panelDesiredSize = child.DesiredSize;
        }

        return panelDesiredSize;
    }

    protected override Size ArrangeOverride(Size finalSize)
    {
        foreach (var child in Children)
        {
            double x = 50;
            double y = 50;

            child.Arrange(new Rect(new Point(x, y), child.DesiredSize));
        }
        
        return finalSize; // 返回最终排列的大小
    }
}
```
