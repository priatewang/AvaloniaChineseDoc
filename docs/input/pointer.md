# 鼠标和指针设备

## 鼠标和指针设备

Avalonia 的操作抽象为指针设备，包括但不限于鼠标、触摸屏、笔等。Avalonia 中的每个控件都提供了以下事件，允许开发者订阅指针设备的移动、点击和滚动事件：

* PointerEnter
* PointerLeave
* PointerMoved
* PointerPressed
* PointerReleased
* PointerWheelChanged

下面是如何在控件上响应鼠标按钮按下事件的示例：

```csharp
control.PointerPressed += (args) =>
{
    var point = args.GetCurrentPoint();
    var x = point.Position.X;
    var y = point.Position.Y;
    if (point.Properties.IsLeftButtonPressed)
    {
        // 左键被按下
    }
    if (point.Properties.IsRightButtonPressed)
    {
        // 右键被按下
    }
}
```

在上面的示例中，坐标 `x` 和 `y` 是相对于窗口原点的。如果您想获取相对于特定控件的坐标，则可以将其传递给 `PointerEventArgs.GetCurrentPoint` 方法，例如：`var pointControlCoords = args.GetCurrentPoint(control);`

## Tapped 事件参数

每个控件还有特殊的手势事件：Tapped 和 DoubleTapped。当指针在控件上按下并释放时，会触发 Tapped 事件。而 DoubleTapped 则在同一位置上连续按下两次指针后触发。允许的大小（第一次和第二次“点击”之间的距离）和时间（它们之间的延迟）取决于平台，通常对于触摸设备而言更大。

#### 参考资料 <a href="#reference" id="reference"></a>

[Control](http://reference.avaloniaui.net/api/Avalonia.Controls/Control/) [PointerEventArgs](http://reference.avaloniaui.net/api/Avalonia.Input/PointerEventArgs/)
