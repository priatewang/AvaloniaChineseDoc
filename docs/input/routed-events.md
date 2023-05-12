# 路由事件

Avalonia 中的大多数事件都是作为路由事件实现的。路由事件是在整个树上引发的事件，而不仅仅是引发事件的控件。

## 什么是路由事件 <a href="#what-is-a-routed-event" id="what-is-a-routed-event"></a>

典型的 Avalonia 应用程序包含许多元素。无论是在代码中创建还是在 XAML 中声明，这些元素都存在于相互关联的元素树中。事件路由可以根据事件定义的不同方向进行，但通常路由是从源元素开始，然后通过元素树“冒泡”上升，直到到达元素树的根（通常是页面或窗口）。如果您之前使用过 HTML DOM，则可能对这个冒泡概念很熟悉。

### 路由事件的顶层场景 <a href="#top-level-scenarios-for-routed-events" id="top-level-scenarios-for-routed-events"></a>

以下是路由事件概念的几个场景的简要概述，以及为什么典型的 CLR 事件不适用于这些场景：

**控件组合和封装：** Avalonia 中的各种控件都有一个丰富的内容模型。例如，您可以在 `Button` 中放置一个图像，这实际上扩展了按钮的可视树。但是，添加的图像不能破坏命中测试行为，即使用户单击技术上属于图像的像素，按钮也必须响应其内容的 `Click`。

**单一处理程序附加点：** 在 Windows Forms 中，您必须多次附加相同的处理程序来处理可能从多个元素引发的事件。路由事件使您只需要附加一次处理程序，就像前面示例中所示的一样，并使用处理程序逻辑来确定事件来自何处，如果必要的话。例如，这可能是先前显示的 XAML 的处理程序：

```csharp
private void CommonClickHandler(object sender, RoutedEventArgs e)
{
  var source = e.Source as Control;
  switch (source.Name)
  {
    case "YesButton":
      // do something here ...
      break;
    case "NoButton":
      // do something ...
      break;
    case "CancelButton":
      // do something ...
      break;
  }
  e.Handled=true;
}
```

**类处理：** 路由事件允许由类定义的静态处理程序。这个类处理程序有机会在任何附加的实例处理程序之前处理事件。

**引用事件而不使用反射：** 某些代码和标记技术需要一种标识特定事件的方法。路由事件创建一个 `RoutedEvent` 字段作为标识符，提供了一种强大的事件标识技术，不需要静态或运行时反射。

### 实现路由事件的方式

路由事件是由 `RoutedEvent` 类实例支持并在 Avalonia 事件系统中注册的 CLR 事件。从注册中获得的 `RoutedEvent` 实例通常作为拥有路由事件的类的 `public` `static` `readonly` 字段成员保留。连接到同名的 CLR 事件（有时称为“包装器”事件）是通过重写 CLR 事件的 `add` 和 `remove` 实现来完成的。通常情况下，`add` 和 `remove` 被留作隐式默认，使用适当的语言特定事件语法来添加和删除该事件的处理程序。路由事件的支持和连接机制在概念上类似于 Avalonia 属性是 CLR 属性，由 `AvaloniaProperty` 类支持并在 Avalonia 属性系统中注册。

以下示例显示了自定义 `Tap` 路由事件的声明，包括 `RoutedEvent` 标识符字段的注册和公开，以及 `Tap` CLR 事件的 `add` 和 `remove` 实现。

```csharp
public class SampleControl: Control
{
  public static readonly RoutedEvent<RoutedEventArgs> TapEvent =
    RoutedEvent.Register<SampleControl, RoutedEventArgs>(nameof(Tap), RoutingStrategies.Bubble);

  // Provide CLR accessors for the event
  public event EventHandler<RoutedEventArgs> Tap
  { 
    add => AddHandler(TapEvent, value);
    remove => RemoveHandler(TapEvent, value);
  }
}
```

### 路由事件处理程序和 XAML

要使用 XAML 添加事件处理程序，您需要将事件名称作为属性声明在作为事件监听器的元素上。属性的值是您实现的处理程序方法的名称，该方法必须存在于代码后台文件的类中。

```markup
<Button Click="b1SetColor">button</Button>
```

添加标准 CLR 事件处理程序的 XAML 语法与添加路由事件处理程序的语法相同，因为您实际上是将处理程序添加到具有路由事件实现的 CLR 事件包装器上。

截至 Avalonia 0.9.x，Avalonia 设计器要求事件处理程序声明为 `public` 方法。我们希望在未来删除此限制。

路由事件有三种路由策略：

* **冒泡（Bubbling）：** 事件源上的事件处理程序被调用，然后将路由事件路由到后续的父元素，直到达到元素树的根。大多数路由事件使用冒泡路由策略。冒泡路由事件通常用于从不同控件或其他 UI 元素报告输入或状态更改。
* **直接（Direct）：** 只有源元素本身有机会响应调用处理程序。这类似于 Windows Forms 用于事件的“路由”。但是，与标准 CLR 事件不同，直接路由事件支持类处理（类处理将在后面的部分中解释）。
* **隧道（Tunneling）：** 最初，会调用元素树根处的事件处理程序。然后，路由事件沿着路线穿过连续的子元素，向路由事件源节点元素（引发路由事件的元素）移动。隧道路由事件通常用于作为控件的组成部分进行合成处理，以便来自组合部分的事件可以被有意地抑制或替换为特定于完整控件的事件。在 Avalonia 中提供的输入事件通常会引发隧道和冒泡事件。

## 为什么使用路由事件？ <a href="#why-use-routed-events" id="why-use-routed-events"></a>

作为应用程序开发人员，您并不总是需要知道或关心您正在处理的事件是否实现为路由事件。路由事件具有特殊的行为，但如果您在引发事件的元素上处理事件，则这种行为基本上是不可见的。

当使用以下任何一种建议的方案时，路由事件变得强大：在公共根处定义公共处理程序，合成自己的控件或定义自己的自定义控件类。

路由事件侦听器和路由事件源不需要在它们的层次结构中共享公共事件。任何控件都可以成为任何路由事件的事件侦听器。因此，您可以使用在整个工作 API 集中可用的完整路由事件集作为概念性 "接口"，使应用程序中的不同元素可以交换事件信息。这种路由事件的 "接口" 概念特别适用于输入事件。

路由事件还可以用于通过元素树进行通信，因为事件的事件数据在路由中传递到每个元素。一个元素可以更改事件数据中的某些内容，并且该更改将在路由中的下一个元素中可用。

除了路由方面，还有两个原因使任何给定的 Avalonia 事件可能被实现为路由事件而不是标准 CLR 事件。如果您正在实现自己的事件，您可能还应考虑这些原则：

* 某些样式和模板特性需要引用的事件成为路由事件。这是前面提到的事件标识符方案。
* 路由事件支持类处理机制，其中类可以指定静态方法，这些方法有机会在任何已注册的实例处理程序可以访问它们之前处理路由事件。这在控件设计中非常有用，因为您的类可以强制执行不能通过在实例上处理事件而意外被抑制的事件驱动类行为。

上述每个考虑因素在本主题的一个单独部分中进行了讨论。

## 添加并实现路由事件的事件处理程序 <a href="#adding-and-implementing-an-event-handler-for-a-routed-event" id="adding-and-implementing-an-event-handler-for-a-routed-event"></a>

要在 XAML 中添加事件处理程序，只需将事件名称作为属性添加到元素中，并将属性值设置为实现适当委托的事件处理程序的名称，如下面的示例所示。

```markup
<Button Click="b1SetColor">button</Button>
```

`b1SetColor` 是实现处理 `Click` 事件的代码的处理程序名称。`b1SetColor` 必须与 `Click` 事件的事件处理程序委托 `RoutedEventHandler<RoutedEventArgs>` 具有相同的签名。路由事件处理程序委托的所有参数中，第一个参数指定添加事件处理程序的元素，第二个参数指定事件数据。

```csharp
void b1SetColor(object sender, RoutedEventArgs args)
{
  // 处理 Click 事件的逻辑
}
```

`RoutedEventHandler<RoutedEventArgs>` 是基本的路由事件处理程序委托。对于专门针对某些控件或场景的路由事件，用于路由事件处理程序的委托也可能会变得更加专门化，以便传输专门的事件数据。例如，在常见的输入场景中，您可能会处理 `PointerPressed` 路由事件。您的处理程序应该实现 `RoutedEventHandler<PointerPressedEventArgs>` 委托。通过使用最具体的委托，您可以在处理程序中处理 `PointerPressedEventArgs` 并读取 `PointerEventArgs.Pointer` 属性，该属性包含有关导致按下的指针的信息。

在通过代码创建的应用程序中添加路由事件处理程序很简单。路由事件处理程序始终可以通过帮助方法 `AddHandler` 添加（这也是现有支持 `add` 的方法的相同方法）。但是，现有的 Avalonia 路由事件通常具有支持 `add` 和 `remove` 逻辑的实现，使得路由事件的处理程序可以通过语言特定的事件语法添加，这是比帮助方法更直观的语法。以下是帮助方法的示例用法：

```csharp
void MakeButton()
{
    Button b2 = new Button();
    b2.AddHandler(Button.ClickEvent, Onb2Click);
}

void Onb2Click(object sender, RoutedEventArgs e)
{
    // 处理 Click 事件的逻辑
}
```

下一个示例展示了 C# 运算符语法：

```csharp
void MakeButton2()
{
  Button b2 = new Button();
  b2.Click += Onb2Click2;
}

void Onb2Click2(object sender, RoutedEventArgs e)
{
  // 处理 Click 事件的逻辑     
}
```

**Handled的概念**

所有路由事件共享一个公共的事件数据基类`RoutedEventArgs`。`RoutedEventArgs`定义了一个布尔类型的`Handled`属性。`Handled`属性的目的是允许沿路线的任何事件处理程序标记路由事件为“已处理”，通过将`Handled`的值设置为`true`。在被沿路线中的一个元素的处理程序处理后，共享的事件数据再次报告给沿路线的每个侦听器。

`Handled`的值会影响路由事件在向下或向上路由时的报告或处理方式。如果路由事件的事件数据中`Handled`为`true`，那么其他元素上监听该路由事件的处理程序通常不再为该特定事件实例调用。这适用于在XAML中附加的处理程序以及通过特定语言的事件处理程序附加语法（如`+=`）添加的处理程序。对于大多数常见的处理程序方案，通过将事件标记为已处理来设置`Handled`为`true`将“停止”隧道路由或冒泡路由的路由，以及在路由中某个点上处理的任何事件的类处理程序。

但是，有一种“handledEventsToo”机制，监听器可以仍然响应路由事件并运行处理程序，即使事件数据中`Handled`为`true`。换句话说，标记事件数据为已处理并不真正停止事件路由。你只能在代码中使用handledEventsToo机制：

* 在代码中，不要使用适用于一般CLR事件的特定语言的事件语法，而是调用Avalonia方法`AddHandler<TEventArgs>(RoutedEvent<TEventArgs>, EventHandler<TEventArgs> handler, RoutingStrategies, bool)`来添加处理程序。将`handledEventsToo`的值指定为`true`。

除了 `Handled` 状态在路由事件中的行为之外，`Handled` 的概念还对应用程序的设计和事件处理程序代码编写有着影响。你可以将 `Handled` 概念化为路由事件公开的简单协议。你如何使用该协议取决于你自己，但是 `Handled` 值的预期使用方式如下所述：

* 如果一个路由事件被标记为已处理，则其他元素沿着该路由的事件处理程序不需要再处理该事件。
* 如果一个路由事件未被标记为已处理，则先前沿着路由的其他侦听器已选择不注册处理程序，或已注册的处理程序选择不操作事件数据并将 `Handled` 设置为 `true`。（或者当前侦听器是路由中的第一个点。）当前侦听器上的处理程序现在有三种可能的操作：
  * 不执行任何操作；事件保持未处理状态，并路由到下一个侦听器。
  * 执行响应事件的代码，但确定所采取的操作不足以标记事件为已处理。事件路由到下一个侦听器。
  * 执行响应事件的代码。在传递给处理程序的事件数据中标记事件为已处理，因为所采取的操作被认为足以标记为已处理。事件仍会路由到下一个侦听器，但带有 `Handled=true` 的事件数据，因此只有 `handledEventsToo` 侦听器有机会调用进一步的处理程序。

这种概念设计得到了前面提到的路由行为的支持：如果先前沿着路由的处理程序已将 `Handled` 设置为 `true`，则更难（虽然在代码或样式中仍然可能）附加将被调用的处理程序的路由事件。

在应用程序中，通常仅在引发路由事件的对象上处理冒泡路由事件，并不关心事件的路由特性。但是，为了防止进一步向上的元素树中的元素也具有相同路由事件的处理程序而导致意外副作用，将路由事件标记为已处理仍然是一个好的实践。

## 类处理程序

如果你定义了一个从 `AvaloniaObject` 派生的类，你可以为该类声明的或继承的任何路由事件定义并附加一个类处理程序。类处理程序会在实例级别的处理程序之前被调用，即使在路由事件到达该元素实例时，该实例上也已经存在实例处理程序。

一些 Avalonia 控件具有对特定路由事件的固有类处理行为。这可能会给人一种该路由事件从未被触发的外观，但实际上它正在进行类处理，如果你使用某些技术，该路由事件仍然可能由实例处理程序处理。此外，许多基类和控件公开了可用于重写类处理行为的虚拟方法。

为了在自己的控件中附加一个类处理程序，请使用静态构造函数中的 `AddClassHandler` 方法：

```csharp
static MyControl()
{
    MyEvent.AddClassHandler<MyControl>((x, e) => x.OnMyEvent(e));
}

protected virtual void OnMyEvent(MyEventArgs e)
{
    // 在这里处理事件。
}
```

## Avalonia 中的附加事件

XAML 语言还定义了一种特殊类型的事件，称为“附加事件”。附加事件允许你将一个特定事件的处理程序添加到任意元素上。处理事件的元素不必定义或继承该附加事件，既不会引发该事件的对象，也不会处理该事件的目标实例必须将其定义为类成员或拥有该类成员。

Avalonia 输入系统广泛使用附加事件。然而，几乎所有这些附加事件都是通过基本元素进行转发的。然后，输入事件会显示为基本元素类的等效非附加路由事件成员。例如，可以在任何给定的 `Control` 上使用 `Tapped` 处理 `Gestures.Tapped` 的基础附加事件，而不必使用 XAML 或代码中的附加事件语法。

## XAML中的合格事件名称

另一种类似于\_typename\_.\_eventname\_附加事件语法的用法，但严格来说不是附加事件用法，是当您附加处理程序用于由子元素引发的路由事件时。您将处理程序附加到共同的父级，以利用事件路由，即使共同的父级可能没有相关的路由事件作为成员。再次考虑以下示例：

```markup
<Border Height="50" Width="300">
  <StackPanel Orientation="Horizontal" Button.Click="CommonClickHandler">
    <Button Name="YesButton">Yes</Button>
    <Button Name="NoButton">No</Button>
    <Button Name="CancelButton">Cancel</Button>
  </StackPanel>
</Border>
```

在这里，父元素监听器（处理程序所添加的位置）是一个`StackPanel`。但是，它正在添加一个处理程序，用于一个由`Button`类声明并将引发的路由事件。`Button` "拥有"事件，但路由事件系统允许附加任何路由事件的处理程序到任何控件实例监听器上，否则这些监听器可以附加到公共语言运行时（CLR）事件。这些合格的事件属性名称的默认xmlns命名空间通常是默认的Avalonia xmlns命名空间，但是您也可以为自定义路由事件指定前缀命名空间。

## 输入事件 <a href="#input-events" id="input-events"></a>

在 Avalonia 平台中，路由事件的一个常见应用是处理输入事件。输入事件通常以一对形式出现，其中一个是冒泡事件，另一个是隧道事件。有时，输入事件只有冒泡版本，或者只有直接路由版本。

Avalonia 的输入事件以这样的方式实现：单个用户输入操作（例如鼠标按键按下）将按顺序引发该对路由事件。首先引发隧道事件并沿着其路由传递，然后引发冒泡事件并沿着其路由传递。这两个事件实际上共享相同的事件数据实例，因为实现类中引发冒泡事件的 `RaiseEvent` 方法调用会监听隧道事件的事件数据，并在新引发的事件中重用它。具有隧道事件处理程序的侦听器有第一次机会标记路由事件已处理（类处理程序优先，然后是实例处理程序）。如果沿隧道传递的元素将路由事件标记为已处理，则已处理的事件数据将发送到冒泡事件，等效冒泡输入事件的典型处理程序将不会被调用。从外观上看，好像已处理的冒泡事件甚至都没有被引发一样。这种处理行为对于控件组合很有用，因为您可能希望所有基于命中测试的输入事件或基于焦点的输入事件都由最终控件报告，而不是由其组合部分报告。最终的控件元素在组合中更靠近根元素，因此有机会首先类处理隧道事件，并在支持控件类的代码的一部分中“替换”该路由事件为更具控件特定的事件。

以下是一个输入事件处理的例子。在以下树形结构示意图中，`leaf element #2` 是一个 `PointerPressed` 事件的来源：

事件处理的顺序如下：

1. 在根元素上引发 `PointerPressed`（隧道）事件。
2. 在中间元素 #1 上引发 `PointerPressed`（隧道）事件。
3. 在源元素 #2 上引发 `PointerPressed`（隧道）事件。
4. 在源元素 #2 上引发 `PointerPressed`（冒泡）事件。
5. 在中间元素 #1 上引发 `PointerPressed`（冒泡）事件。
6. 在根元素上引发 `PointerPressed`（冒泡）事件。

路由事件处理程序委托提供了两个对象的引用：引发事件的对象和调用处理程序的对象。调用处理程序的对象是由`sender`参数报告的对象。首次引发事件的对象由事件数据中的`Source`属性报告。路由事件仍然可以由同一对象引发和处理，在这种情况下，`sender`和`Source`是相同的（这是事件处理示例列表中的第3步和第4步的情况）。

由于隧道和冒泡，父元素会接收其子元素之一作为`Source`的输入事件。如果重要的是知道源元素是什么，可以通过访问`Source`属性来确定源元素。

通常，一旦将输入事件标记为`Handled`，将不再调用进一步的处理程序。通常，应在调用处理程序以处理输入事件的应用程序特定逻辑之后，尽快将输入事件标记为已处理。

关于`Handled`状态的这个一般性陈述的例外情况是，已注册为故意忽略事件数据的`Handled`状态的输入事件处理程序仍将沿任一路线被调用。

某些类选择使用类处理某些输入事件，通常旨在重新定义控件中特定的用户驱动输入事件的含义并引发新事件。
