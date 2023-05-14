# 图形和动画

## 图形

Avalonia引入了一组广泛、可伸缩和灵活的图形功能，具有以下优势：

* 分辨率无关和设备无关的图形。Avalonia图形系统的基本测量单位是设备无关像素（DIP），无论实际屏幕分辨率如何，它都是1/96英寸，并为分辨率无关和设备无关的渲染提供基础。每个设备无关像素会自动缩放以匹配其所呈现的系统的每英寸点数（dpi）设置。
* 改进的精度。Avalonia坐标系统使用双精度浮点数而不是单精度浮点数进行测量。变换和不透明度值也以双精度表示。
* 高级图形和动画支持。Avalonia通过为您管理动画场景简化了图形编程；无需担心场景处理、渲染循环和双线性插值。此外，Avalonia还提供了命中测试支持和完全的alpha混合支持。
* Skia。默认情况下，Avalonia使用[Skia渲染引擎](https://skia.org/)，这是一个强大的渲染引擎，为Google Chrome和Chrome OS、Android、Mozilla Firefox和Firefox OS以及许多其他产品提供动力。

## 2D形状和几何图形

Avalonia提供了一个常见的矢量绘制2D形状库，例如`Ellipse`、`Line`、`Path`、`Polygon`和`Rectangle`。

```xaml
<Canvas Background="Yellow" Width="300" Height="400">
    <Rectangle Fill="Blue" Width="63" Height="41" Canvas.Left="40" Canvas.Top="31">
        <Rectangle.OpacityMask>
            <LinearGradientBrush StartPoint="0%,0%" EndPoint="100%,100%">
                <LinearGradientBrush.GradientStops>
                    <GradientStop Offset="0" Color="Black"/>
                    <GradientStop Offset="1" Color="Transparent"/>
                </LinearGradientBrush.GradientStops>
            </LinearGradientBrush>
        </Rectangle.OpacityMask>     
    </Rectangle>
    <Ellipse Fill="Green" Width="58" Height="58" Canvas.Left="88" Canvas.Top="100"/>
    <Path Fill="Orange" Data="M 0,0 c 0,0 50,0 50,-50 c 0,0 50,0 50,50 h -50 v 50 l -50,-50 Z" Canvas.Left="30" Canvas.Top="250"/>
    <Path Fill="OrangeRed" Canvas.Left="180" Canvas.Top="250">
        <Path.Data>
            <PathGeometry>
                <PathFigure StartPoint="0,0" IsClosed="True">
                    <QuadraticBezierSegment Point1="50,0" Point2="50,-50" />
                    <QuadraticBezierSegment Point1="100,-50" Point2="100,0" />
                    <LineSegment Point="50,0" />
                    <LineSegment Point="50,50" />
                </PathFigure>
            </PathGeometry>
        </Path.Data>
    </Path>
    <Line StartPoint="120,185" EndPoint="30,115" Stroke="Red" StrokeThickness="2"/>
    <Polygon Points="75,0 120,120 0,45 150,45 30,120" Stroke="DarkBlue" StrokeThickness="1" Fill="Violet" Canvas.Left="150" Canvas.Top="31"/>
    <Polyline Points="0,0 65,0 78,-26 91,39 104,-39 117,13 130,0 195,0" Stroke="Brown" Canvas.Left="30" Canvas.Top="350"/>
</Canvas>
```

## 动画

Avalonia的动画支持允许您使控件增长、震动、旋转和淡出，以创建有趣的页面转换等。Avalonia使用类似CSS的动画系统，支持[属性过渡](https://docs.avaloniaui.net/docs/animations/transitions)和[关键帧动画](https://docs.avaloniaui.net/docs/animations/keyframe-animations)。
