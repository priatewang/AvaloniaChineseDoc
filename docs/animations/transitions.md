# 过渡

在 Avalonia 中，过渡效果的设计也受到了 CSS 动画的重大启发。它们会监听目标属性值的任何变化，并根据其参数对变化进行动画处理。可以通过 `Transitions` 属性在任何 `Control` 上定义它们：

```markup
<Window xmlns="https://github.com/avaloniaui">
    <Window.Styles>
        <Style Selector="Rectangle.red">
            <Setter Property="Height" Value="100"/>
            <Setter Property="Width" Value="100"/>
            <Setter Property="Fill" Value="Red"/>
            <Setter Property="Opacity" Value="0.5"/>
        </Style>
        <Style Selector="Rectangle.red:pointerover">
            <Setter Property="Opacity" Value="1"/>
        </Style>
    </Window.Styles>

    <Rectangle Classes="red">
        <Rectangle.Transitions>
            <Transitions>
                <DoubleTransition Property="Opacity" Duration="0:0:0.2"/>
            </Transitions>
        </Rectangle.Transitions>
    </Rectangle>

</Window>
```

上面的示例将监听 `Rectangle` 的 `Opacity` 属性的变化，当值发生变化时，会平滑地将旧值转换为新值，耗时 2 秒钟。

过渡效果也可以在任何样式中使用，只需使用 `Transitions` 作为目标属性，并将它们封装在 `Transitions` 对象中，如下所示：

```markup
<Window xmlns="https://github.com/avaloniaui">
    <Window.Styles>
        <Style Selector="Rectangle.red">
            <Setter Property="Height" Value="100"/>
            <Setter Property="Width" Value="100"/>
            <Setter Property="Fill" Value="Red"/>
            <Setter Property="Opacity" Value="0.5"/>
            <Setter Property="Transitions">
                <Transitions>
                    <DoubleTransition Property="Opacity" Duration="0:0:0.2"/>
                </Transitions>
            </Setter>
        </Style>
        <Style Selector="Rectangle.red:pointerover">
            <Setter Property="Opacity" Value="1"/>
        </Style>
    </Window.Styles>

    <Rectangle Classes="red"/>

</Window>
```

每个过渡效果都有一个 `Property`、`Delay`、`Duration` 和一个可选的 `Easing` 属性。

`Property` 指的是过渡效果的目标，用于监听和对目标属性的值进行动画处理。

`Delay` 指的是应用过渡效果之前等待的时间量。

`Duration` 指的是过渡效果播放的时间量。

缓动函数与[关键帧动画](https://docs.avaloniaui.net/docs/animations/keyframe-animations#easings)中描述的一样。

可用的过渡效果类型如下。必须根据正在动画的属性的类型使用正确的类型。

* `BoxShadowsTransition`：用于 `BoxShadows` 目标属性
* `BrushTransition`：用于 `IBrush` 目标属性
* `ColorTransition`：用于 `Color` 目标属性
* `CornerRadiusTransition`：用于 `CornerRadius` 目标属性
* `DoubleTransitions`：用于 `double` 目标属性
* `FloatTransitions`：用于 `float` 目标属性
* `IntegerTransitions`：用于 `int` 目标属性
* `PointTransition`：用于 `Point` 目标属性
* `SizeTransition`：用于 `Size` 目标属性
* `ThicknessTransition`：用于 `Thickness` 目标属性
* `TransformOperationsTransition`：用于 `ITransform` 目标属性
* `VectorTransition`：用于 `Vector` 目标属性

可以对使用类似CSS语法应用的渲染变换进行过渡动画。下面的示例展示了一个Border，当鼠标指针悬停在它上面时，它会旋转45度：

{% tabs %}
{% tab title="XAML" %}
```markup
<Border Width="100" Height="100" Background="Red">
    <Border.Styles>
        <Style Selector="Border">
            <Setter Property="RenderTransform" Value="rotate(0)"/>
        </Style>
        <Style Selector="Border:pointerover">
            <Setter Property="RenderTransform" Value="rotate(45deg)"/>
        </Style>
    </Border.Styles>
    <Border.Transitions>
        <Transitions>
            <TransformOperationsTransition Property="RenderTransform" Duration="0:0:1"/>
        </Transitions>
    </Border.Transitions>
</Border>
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Border
{
    Width = 100,
    Height = 100,
    Background = Brushes.Red,
    Styles =
    {
        new Style(x => x.OfType<Border>())
        {
            Setters =
            {
                new Setter(
                    Border.RenderTransformProperty,
                    TransformOperations.Parse("rotate(0)"))
            },
        },
        new Style(x => x.OfType<Border>().Class(":pointerover"))
        {
            Setters =
            {
                new Setter(
                    Border.RenderTransformProperty,
                    TransformOperations.Parse("rotate(45deg)"))
            },
        },
    },
    Transitions = new Transitions
    {
        new TransformOperationsTransition
        {
            Property = Border.RenderTransformProperty,
            Duration = TimeSpan.FromSeconds(1),
        }
    }
};
```
{% endtab %}
{% endtabs %}

可用的过渡动画类型为：

| 过渡动画         | 示例                                       | 可接受的单位                       |
| ------------ | ---------------------------------------- | ---------------------------- |
| `translate`  | `translate(10px)`，`translate(0px, 10px)` | `px`                         |
| `translateX` | `translateX(10px)`                       | `px`                         |
| `translateY` | `translateY(10px)`                       | `px`                         |
| `scale`      | `scale(10)`，`scale(0, 10)`               |                              |
| `scaleX`     | `scaleX(10)`                             |                              |
| `scaleY`     | `scaleY(10)`                             |                              |
| `skew`       | `skew(90deg)`，`skew(0, 90deg)`           | `deg`, `grad`, `rad`, `turn` |
| `skewX`      | `skewX(90deg)`                           | `deg`, `grad`, `rad`, `turn` |
| `skewY`      | `skewY(90deg)`                           | `deg`, `grad`, `rad`, `turn` |
| `rotate`     | `rotate(90deg)`                          | `deg`, `grad`, `rad`, `turn` |
| `matrix`     | `matrix(1,2,3,4,5,6)`                    |                              |

{% hint style="info" %}
Avalonia还支持WPF-style的渲染变换，如`RotateTransform`，`ScaleTransform`等。这些变换不能进行过渡动画：如果要对渲染变换应用过渡动画，请始终使用类似CSS的格式。
{% endhint %}
