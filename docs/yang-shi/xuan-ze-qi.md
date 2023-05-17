# 选择器

## OfType <a href="#oftype" id="oftype"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="Button">
<Style Selector="local|Button">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<Button>());
new Style(x => x.OfType(typeof(Button)));
```
{% endtab %}
{% endtabs %}

按类型选择控件。上面的第一个示例选择了`Avalonia.Controls.Button`类。要在类型中包含XAML命名空间，请使用`|`字符分隔命名空间和类型。

该选择器不匹配派生类型。要匹配派生类型，请使用`Is`选择器。

{% hint style="info" %}
注意，对象的类型实际上是通过查看其`IStyleable.StyleKey`属性确定的。默认情况下，它只返回当前实例的类型，但是如果，例如，您希望从`Button`继承的控件被视为`Button`，那么您可以在类上实现`IStyleable.StyleKey`属性，返回`typeof(Button)`。
{% endhint %}

## Name <a href="#name" id="name"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="#myButton">
<Style Selector="Button#myButton">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.Name("myButton"));
new Style(x => x.OfType<Button>().Name("myButton"));
```
{% endtab %}
{% endtabs %}

按其 [`Name`](http://reference.avaloniaui.net/api/Avalonia/StyledElement/2362746E) 属性选择控件，并使用`#`字符。

## Class <a href="#class" id="class"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="Button.large">
<Style Selector="Button.large:focus">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<Button>().Class("large"));
new Style(x => x.OfType<Button>().Class("large").Class(":focus"));
```
{% endtab %}
{% endtabs %}

选择具有指定样式类的控件。多个类应使用`.`字符分隔，对于伪类，使用`:`字符。如果指定了多个类，则控件必须具有所有请求的类才能匹配。

## Is <a href="#is" id="is"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector=":is(Button)">
<Style Selector=":is(local|Button)">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.Is<Button>());
new Style(x => x.Is(typeof(Button)));
```
{% endtab %}
{% endtabs %}

这与`OfType`选择器非常相似，但它还匹配派生类型。

{% hint style="info" %}
同样，对象的类型实际上是通过查看其`IStyleable.StyleKey`属性来确定的。
{% endhint %}

## Child <a href="#child" id="child"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="StackPanel > Button">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<StackPanel>().Child().OfType<Button>());
```
{% endtab %}
{% endtabs %}

使用`>`字符来定义子选择器。此选择器匹配\_逻辑树中的直接子级\_，因此在上面的示例中，选择器将匹配任何是`StackPanel`的直接逻辑子级的`Button`。

## Descendant <a href="#descendant" id="descendant"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="StackPanel Button">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<StackPanel>().Descendant().OfType<Button>());
```
{% endtab %}
{% endtabs %}

当两个选择器之间用空格分隔时，选择器将匹配逻辑树中的后代。因此，在这种情况下，选择器将匹配任何是`StackPanel`的逻辑后代的`Button`。

## PropertyEquals <a href="#propertyequals" id="propertyequals"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="Button[IsDefault=true]">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<Button>().PropertyEquals(Button.IsDefaultProperty, true));
```
{% endtab %}
{% endtabs %}

匹配具有指定属性设置为指定值的任何控件。

{% hint style="info" %}
\*\*注意：\*\*在XAML中使用选择器时，`AttachedProperty`必须用括号括起来。

```markup
<Style Selector="TextBlock[(Grid.Row)=0]">
```
{% endhint %}

{% hint style="info" %}
\*\*注意：\*\*在XAML中使用选择器时，属性必须支持[`TypeConverter`](https://learn.microsoft.com/dotnet/api/system.componentmodel.typeconverter)。
{% endhint %}

## Template <a href="#template" id="template"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="Button /template/ ContentPresenter">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<Button>().Template().OfType<ContentPresenter>());
```
{% endtab %}
{% endtabs %}

匹配控件模板中的控件。此处列出的所有其他选择器均适用于逻辑树。如果要选择控件模板中的控件，则必须使用此选择器。该示例选择`Button`模板中的`ContentPresenter`控件。

## Not <a href="#not" id="not"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="TextBlock:not(.h1)">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<TextBlock>().Not(y => y.Class("h1")));
```
{% endtab %}
{% endtabs %}

否定内部选择器。

## Or <a href="#or" id="or"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="TextBlock, Button">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => Selectors.Or(x.OfType<TextBlock>(), x.OfType<Button>()))
```
{% endtab %}
{% endtabs %}

找到与任何这些选择器之一匹配的元素。每个选择器之间用逗号","分隔。

## Nth Child <a href="#nth-child" id="nth-child"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="TextBlock:nth-child(2n+3)">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<TextBlock>().NthChild(2, 3));
```
{% endtab %}
{% endtabs %}

根据元素在同级兄弟元素中的位置匹配元素。

## Nth Last Child <a href="#nth-last-child" id="nth-last-child"></a>

{% tabs %}
{% tab title="XAML" %}
```markup
<Style Selector="TextBlock:nth-last-child(2n+3)">
```
{% endtab %}

{% tab title="C#" %}
```csharp
new Style(x => x.OfType<TextBlock>().NthLastChild(2, 3));
```
{% endtab %}
{% endtabs %}

根据元素在同级兄弟元素中的位置（从末尾计数）匹配元素。

## Nth Child and Nth Last Child Syntax

`:nth-child()`和`:nth-last-child()`接受一个参数，描述了在同级兄弟元素列表中匹配元素索引的模式。元素索引从**1开始**。下面的示例使用了`:nth-child()`，但也适用于`:nth-last-child()`。

### 关键字表示法

`odd`：表示索引在同级兄弟元素系列中为奇数的元素：1、3、5等。

`even`：表示索引在同级兄弟元素系列中为偶数的元素：2、4、6等。

### 函数表示法

`An+B`：表示在元素索引的列表中，匹配自定义数字模式的元素，该模式由An+B定义，其中：

* `A`是一个整数步长，
* `B`是一个整数偏移量，
* `n`是非负整数，从0开始。

可以理解为\_从第`B`个开始的每个第`A`个元素\_

### 函数表示法示例

以下是一些使用函数表示法的示例：

| 示例                 | 表示方式                                                                        |
| ------------------ | --------------------------------------------------------------------------- |
| `:nth-child(odd)`  | 奇数元素：**1**、**3**、**5**等                                                     |
| `:nth-child(even)` | 偶数元素：**2**、**4**、**6**等                                                     |
| `:nth-child(2n+1)` | 奇数元素：**1**_(2×0+1)_, **3**_(2×1+1)_, **5**\_(2×2+1)\_等，相当于`:nth-child(odd)` |
| `:nth-child(2n)`   | 偶数元素：**2**_(2×1)_, **4**_(2×2)_, **6**\_(2×3)\_等，相当于`:nth-child(even)`      |
| `:nth-child(3n+1)` | 每隔3个元素的第一个元素：**1**_(3×0+1)_, **4**_(3×1+1)_, **7**\_(3×2+1)\_等              |
| `:nth-child(3n+2)` | 每隔3个元素的第二个元素：**2**_(3×0+2)_, **5**_(3×1+2)_, **8**\_(3×2+2)\_等              |
| `:nth-child(3n)`   | 每隔3个元素的第三个元素：**3**_(3×1)_, **6**_(3×2)_, **9**\_(3×3)\_等                    |

\### 在线的 nth-child & nth-last-child 测试器

您可以使用下面的链接来轻松评估`nth-child`和`nth-last-child`： [nth-child-tester](https://css-tricks.com/examples/nth-child-tester/)
