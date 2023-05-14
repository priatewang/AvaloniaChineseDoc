# 页面过渡

`PageTransitions` 用于在两个视图之间呈现过渡效果，例如在 [Carousel](https://docs.avaloniaui.net/docs/controls/carousel) 或 [TransitioningContentControl](https://docs.avaloniaui.net/docs/controls/TransitioningContentControl) 中。

{% hint style="warning" %}
必须在使用过渡效果之前设置持续时间，并且持续时间必须大于0。否则，会出现错误。
{% endhint %}

## 内置 PageTransitions

### CrossFade

`CrossFade` 通过动画设置透明度来淡出当前视图并淡入新视图。

{% tabs %}
{% tab title="XAML" %}
```markup
<CrossFade Duration="0:00:00.500" />
```
{% endtab %}

{% tab title="C#" %}
```csharp
var transition = new CrossFade(TimeSpan.FromMilliseconds(500));
```
{% endtab %}
{% endtabs %}

#### 源代码

[CrossFade.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Visuals/Animation/CrossFade.cs)

#### 参考

[CrossFade](http://reference.avaloniaui.net/api/Avalonia.Animation/CrossFade/)

### PageSlide

`PageSlide` 水平或垂直地滑动内容。您可以通过 `Orientation` 属性指定滑动轴。默认值为 `Horizontal`。

{% tabs %}
{% tab title="XAML" %}
```markup
<PageSlide Duration="0:00:00.500" Orientation="Vertical" />
```
{% endtab %}

{% tab title="C#" %}
```csharp
var transition = new PageSlide(TimeSpan.FromMilliseconds(500), PageSlide.SlideAxis.Vertical);
```
{% endtab %}
{% endtabs %}

#### 源代码

[PageSlide.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Visuals/Animation/PageSlide.cs)

#### 参考

[PageSlide](http://reference.avaloniaui.net/api/Avalonia.Animation/PageSlide/)

### CompositePageTransition

`CompositePageTransition` 用于创建几个不同过渡效果的组合过渡效果。下面的示例创建了一个过渡效果，将视图对角线滑动（水平和垂直同时）并淡出和淡入视图。

{% tabs %}
{% tab title="XAML" %}
```markup
<CompositePageTransition>
    <CrossFade Duration="0:00:00.500" />
    <PageSlide Duration="0:00:00.500" Orientation="Horizontal" />
    <PageSlide Duration="0:00:00.500" Orientation="Vertical" />
</CompositePageTransition>
```
{% endtab %}

{% tab title="C#" %}
```csharp
var compositeTransition = new CompositePageTransition();
compositeTransition.PageTransitions.Add(new PageSlide(TimeSpan.FromMilliseconds(500), PageSlide.SlideAxis.Vertical));
compositeTransition.PageTransitions.Add(new PageSlide(TimeSpan.FromMilliseconds(500), PageSlide.SlideAxis.Horizontal));
compositeTransition.PageTransitions.Add(new CrossFade(TimeSpan.FromMilliseconds(500)));
```
{% endtab %}
{% endtabs %}

#### 源代码

[CompositePageTransition.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Visuals/Animation/CompositePageTransition.cs)

#### 参考

[CompositePageTransition](http://reference.avaloniaui.net/api/Avalonia.Animation/CompositePageTransition/)

## 自定义页面过渡效果

您还可以通过实现 `IPageTransition` 接口来创建自己的 `PageTransition`。

该接口有一个成员需要实现：

```csharp
public Task Start(Visual? from, Visual? to, bool forward, CancellationToken cancellationToken)
{
    // 在此设置过渡效果。
}
```

### 示例

以下示例将缩小旧视图并在垂直方向上放大新视图。

```csharp
public class CustomTransition : IPageTransition
{
    /// <summary>
    /// Initializes a new instance of the <see cref="CustomTransition"/> class.
    /// </summary>
    public CustomTransition()
    {
    }

    /// <summary>
    /// Initializes a new instance of the <see cref="CustomTransition"/> class.
    /// </summary>
    /// <param name="duration">The duration of the animation.</param>
    public CustomTransition(TimeSpan duration)
    {
        Duration = duration;
    }

    /// <summary>
    /// Gets the duration of the animation.
    /// </summary>
    public TimeSpan Duration { get; set; }

    public async Task Start(Visual from, Visual to, bool forward, CancellationToken cancellationToken)
    {
        if (cancellationToken.IsCancellationRequested)
        {
            return;
        }

        var tasks = new List<Task>();
        var parent = GetVisualParent(from, to);
        var scaleYProperty = ScaleTransform.ScaleYProperty;

        if (from != null)
        {
            var animation = new Animation
            {
                FillMode = FillMode.Forward,
                Children =
                {
                    new KeyFrame
                    {
                        Setters = { new Setter { Property = scaleYProperty, Value = 1d } },
                        Cue = new Cue(0d)
                    },
                    new KeyFrame
                    {
                        Setters =
                        {
                            new Setter
                            {
                                Property = scaleYProperty,
                                Value = 0d
                            }
                        },
                        Cue = new Cue(1d)
                    }
                },
                Duration = Duration
            };
            tasks.Add(animation.RunAsync(from, null, cancellationToken));
        }

        if (to != null)
        {
            to.IsVisible = true;
            var animation = new Animation
            {
                FillMode = FillMode.Forward,
                Children =
                {
                    new KeyFrame
                    {
                        Setters =
                        {
                            new Setter
                            {
                                Property = scaleYProperty,
                                Value = 0d
                            }
                        },
                        Cue = new Cue(0d)
                    },
                    new KeyFrame
                    {
                        Setters = { new Setter { Property = scaleYProperty, Value = 1d } },
                        Cue = new Cue(1d)
                    }
                },
                Duration = Duration
            };
            tasks.Add(animation.RunAsync(to, null, cancellationToken));
        }

        await Task.WhenAll(tasks);

        if (from != null && !cancellationToken.IsCancellationRequested)
        {
            from.IsVisible = false;
        }
    }

    /// <summary>
    /// Gets the common visual parent of the two control.
    /// </summary>
    /// <param name="from">The from control.</param>
    /// <param name="to">The to control.</param>
    /// <returns>The common parent.</returns>
    /// <exception cref="ArgumentException">
    /// The two controls do not share a common parent.
    /// </exception>
    /// <remarks>
    /// Any one of the parameters may be null, but not both.
    /// </remarks>
    private static IVisual GetVisualParent(IVisual? from, IVisual? to)
    {
        var p1 = (from ?? to)!.VisualParent;
        var p2 = (to ?? from)!.VisualParent;

        if (p1 != null && p2 != null && p1 != p2)
        {
            throw new ArgumentException("Controls for PageSlide must have same parent.");
        }

        return p1 ?? throw new InvalidOperationException("Cannot determine visual parent.");
    }
}
```

![Custom Transition Example](E:%5CRepos%5CAvaloniaChineseDoc.gitbook%5Cassets%5CTransitioningContentControl\_03.webp)

#### Source code

[IPageTransition.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Visuals/Animation/IPageTransition.cs)

#### Reference

[IPageTransition](http://reference.avaloniaui.net/api/Avalonia.Animation/IPageTransition/)
