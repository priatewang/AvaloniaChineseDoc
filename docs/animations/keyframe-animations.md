# 关键帧动画

Avalonia 中的关键帧动画受到 CSS 动画的启发。它们可以用于动画控件上的任何数量的属性，使用任意数量的关键帧来定义每个属性必须经过的状态。关键帧动画可以任意次数地运行，以任何方向运行。

## 定义关键帧动画 <a href="#defining-a-keyframe-animation" id="defining-a-keyframe-animation"></a>

关键帧动画使用样式来应用。可以通过将 `Animation` 对象添加到 `Style.Animation` 属性来定义任何样式：

```markup
<Window xmlns="https://github.com/avaloniaui">
    <Window.Styles>
        <Style Selector="Rectangle.red">
            <Setter Property="Height" Value="100"/>
            <Setter Property="Width" Value="100"/>
            <Setter Property="Fill" Value="Red"/>
            <Style.Animations>
                <Animation Duration="0:0:1"> 
                    <KeyFrame Cue="0%">
                        <Setter Property="Opacity" Value="0.0"/>
                    </KeyFrame>
                    <KeyFrame Cue="100%">
                        <Setter Property="Opacity" Value="1.0"/>
                    </KeyFrame>
                </Animation>
            </Style.Animations>
        </Style>
    </Window.Styles>

    <Rectangle Classes="red"/>
</Window>
```

上面的示例将目标 `Control` 按照其 [选择器](https://docs.avaloniaui.net/docs/styling/selectors) 进行动画处理。当控件加载时立即运行。

## 触发动画 <a href="#triggering-animations" id="triggering-animations"></a>

与 WPF 的 `Triggers` 不同，XAML 中定义的动画依赖于 [选择器](https://docs.avaloniaui.net/docs/styling/selectors) 来进行触发行为。选择器可以始终适用于一个控件，或者它们可以有条件地适用（例如，如果控件有应用样式类）。

如果选择器不是有条件的，则动画将在匹配的 `Control` 加载到视觉树中时触发。否则，动画将在其选择器被激活时运行。当选择器不再匹配时，当前运行的动画将被取消。

## `KeyFrames` <a href="#keyframes" id="keyframes"></a>

`KeyFrame` 对象定义目标 `Setter` 对象应该何时应用于目标 `Control` 上，并在之间进行值插值。

`KeyFrame` 对象的 `Cue` 属性基于父动画的 `Duration`，可以是绝对时间索引（即 `"0:0:1"`）或动画 `Duration` 的百分比（即 `"0%"`，`"100%"`）。但是，`Cue` 的值不应超过指定的 `Duration`。

所有 `Animation` 对象应该至少包含一个具有目标属性和值的 `Setter`。

也可以通过在所需的 `KeyFrame` 上添加其他 `Setter` 对象来在单个动画中动画多个属性：

```xaml
<Animation Duration="0:0:1"> 
    <KeyFrame Cue="0%">
        <Setter Property="Opacity" Value="0.0"/>
        <Setter Property="RotateTransform.Angle" Value="0.0"/>
    </KeyFrame>
    <KeyFrame Cue="100%">
        <Setter Property="Opacity" Value="1.0"/>
        <Setter Property="RotateTransform.Angle" Value="90.0"/>
    </KeyFrame>
</Animation>
```

## 延迟 <a href="#delay" id="delay"></a>

您可以通过在`Animation`的`Delay`属性上定义所需的延迟时间来添加延迟：

```markup
<Animation Duration="0:0:1"
           Delay="0:0:1"> 
    ...
</Animation>
```

## 重复 <a href="#repeat" id="repeat"></a>

您可以在`Animation`的`IterationCount`属性上设置以下重复行为。

| 值          | 描述      |
| ---------- | ------- |
| `0` 到 N    | 播放 N 次。 |
| `INFINITE` | 无限重复。   |

## 播放方向 <a href="#playback-direction" id="playback-direction"></a>

`PlaybackDirection`属性定义动画如何播放，包括重复。

以下表格描述了可能的行为：

| 值                  | 描述              |
| ------------------ | --------------- |
| `Normal`           | 正常播放动画。         |
| `Reverse`          | 反向播放动画。         |
| `Alternate`        | 先正向播放动画，然后反向播放。 |
| `AlternateReverse` | 先反向播放动画，然后正向播放。 |

## 值填充模式 <a href="#value-fill-modes" id="value-fill-modes"></a>

`FillMode`属性定义在运行动画和动画之间的延迟时，动画的第一个或最后一个插值值是否保留。

以下表格描述了可能的行为：

| 值          | 描述                         |
| ---------- | -------------------------- |
| `None`     | 值不会在动画之后保留，动画延迟时也不会应用第一个值。 |
| `Forward`  | 最后一个插值值将保留到目标属性。           |
| `Backward` | 第一个插值值将在动画延迟时显示。           |
| `Both`     | 将应用`Forward`和`Backward`行为。 |

## 缓动 <a href="#easings" id="easings"></a>

可以通过将所需函数的名称设置为`Animation`的`Easing`属性来设置缓动函数：

```markup
<Animation Duration="0:0:1"
           Delay="0:0:1"
           Easing="BounceEaseIn"> 
    ...
</Animation>
```

您还可以像这样添加自定义缓动函数类：

```markup
<Animation Duration="0:0:1"
           Delay="0:0:1">
    <Animation.Easing>
        <local:YourCustomEasingClassHere/>
    </Animation.Easing> 
    ...
</Animation>
```

以下列表包含内置缓动函数。

* LinearEasing（默认）
* BackEaseIn
* BackEaseInOut
* BackEaseOut
* BounceEaseIn
* BounceEaseInOut
* BounceEaseOut
* CircularEaseIn
* CircularEaseInOut
* CircularEaseOut
* CubicEaseIn
* CubicEaseInOut
* CubicEaseOut
* ElasticEaseIn
* ElasticEaseInOut
* ElasticEaseOut
* ExponentialEaseIn
* ExponentialEaseInOut
* ExponentialEaseOut
* QuadraticEaseIn
* Quadr
