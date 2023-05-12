# 定义属性

如果你正在创建一个控件，你会想要在你的控件上定义属性。你可以通过定义 `AvaloniaProperty` 来实现。Avalonia 属性由两部分组成：属性定义和属性的 CLR Getter/Setter。

### 注册样式属性 <a href="#registering-styled-properties" id="registering-styled-properties"></a>

除非你有充分的理由不这样做，否则你应该将控件上的属性定义为 _样式属性_。样式属性能够确保你的属性能够与 Avalonia 的[样式系统](https://docs.avaloniaui.net/docs/styling)正确地工作。

你可以通过调用 `AvaloniaProperty.Register` 来注册一个样式属性，并将结果存储在一个 `static readonly` 字段中。然后，你需要创建一个标准的 C# 属性来访问它。

以下是 `Border` 控件如何定义它的 `Background` 属性的代码：

```csharp
public static readonly StyledProperty<Brush> BackgroundProperty =
    AvaloniaProperty.Register<Border, Brush>(nameof(Background));

public Brush Background
{
    get { return GetValue(BackgroundProperty); }
    set { SetValue(BackgroundProperty, value); }
}
```

`AvaloniaProperty.Register` 方法还接受一些其他参数：

* `defaultValue`: 这为属性设置了默认值。请确保只在此处传递值类型和不可变类型，因为传递引用类型将导致同一对象在注册属性的所有实例上使用。
* `inherits`: 指定该属性的默认值是否应该来自于父控件。
* `defaultBindingMode`: 属性的默认绑定模式。可以设置为 `OneWay`、`TwoWay`、`OneTime` 或 `OneWayToSource`。
* `validate`: 一种类型为 `Func<TOwner, TValue, TValue>` 的验证/强制转换函数。该函数接受正在设置该属性的类的实例以及该值，并返回强制转换后的值，或针对无效值抛出异常。

> 样式属性类似于其他 XAML 框架中的 `DependencyProperty`。

> 属性的命名约定及其后备的 AvaloniaProperty 字段的命名是重要的。该字段的名称始终为该属性的名称，附加了后缀 `Property`。

### 在其他类中使用 `StyledProperty` <a href="#using-a-styledproperty-on-another-class" id="using-a-styledproperty-on-another-class"></a>

有时，你想要添加到你的控件中的属性已经存在于其他控件中，比如 `Background`。为了在注册在另一个控件上定义的属性时，你需要调用 `StyledProperty.AddOwner`：

```csharp
public static readonly StyledProperty<IBrush> BackgroundProperty =
    Border.BackgroundProperty.AddOwner<Panel>();

public Brush Background
{
    get { return GetValue(BackgroundProperty); }
    set { SetValue(BackgroundProperty, value); }
}
```

> 注意：与 WPF/UWP 不同，必须在类上注册属性，否则它不能在该类的对象上设置。但这在未来可能会改变。

### 只读属性 <a href="#readonly-properties" id="readonly-properties"></a>

要创建只读属性，可以使用 `AvaloniaProperty.RegisterDirect` 方法。以下是 `Visual` 注册只读 `Bounds` 属性的方法：

```csharp
public static readonly DirectProperty<Visual, Rect> BoundsProperty =
    AvaloniaProperty.RegisterDirect<Visual, Rect>(
        nameof(Bounds),
        o => o.Bounds);

private Rect _bounds;

public Rect Bounds
{
    get { return _bounds; }
    private set { SetAndRaise(BoundsProperty, ref _bounds, value); }
}
```

可以看到，只读属性被存储为对象上的字段。在注册属性时，传递了一个 getter，该 getter 通过 `GetValue` 访问属性值，然后使用 `SetAndRaise` 通知监听器有关属性更改的情况。

### 附加属性 <a href="#attached-properties" id="attached-properties"></a>

[附加属性](https://docs.avaloniaui.net/docs/data-binding/creating-and-binding-attached-properties) 与样式属性几乎相同，只是它们是使用 `RegisterAttached` 方法注册的，并且它们的访问器被定义为静态方法。

下面是 `Grid` 定义其 `Grid.Column` 附加属性的方法：

```csharp
public static readonly AttachedProperty<int> ColumnProperty =
    AvaloniaProperty.RegisterAttached<Grid, Control, int>("Column");

public static int GetColumn(Control element)
{
    return element.GetValue(ColumnProperty);
}

public static void SetColumn(Control element, int value)
{
    element.SetValue(ColumnProperty, value);
}
```

### 直接 AvaloniaProperties <a href="#direct-avaloniaproperties" id="direct-avaloniaproperties"></a>

正如其名称所示，`RegisterDirect` 不仅用于注册只读属性。您还可以将 _setter_ 传递给 `RegisterDirect`，将标准 C# 属性公开为 Avalonia 属性。

使用 `AvaloniaProperty.Register` 注册的 `StyledProperty` 维护了一个优先级列表，该列表允许样式工作。但是，这对于许多属性来说太过复杂，例如 `ItemsControl.Items` - 这将永远不会被样式化，样式属性的开销是不必要的。

下面是 `ItemsControl.Items` 的注册方法：

```csharp
public static readonly DirectProperty<ItemsControl, IEnumerable> ItemsProperty =
    AvaloniaProperty.RegisterDirect<ItemsControl, IEnumerable>(
        nameof(Items),
        o => o.Items,
        (o, v) => o.Items = v);

private IEnumerable _items = new AvaloniaList<object>();

public IEnumerable Items
{
    get { return _items; }
    set { SetAndRaise(ItemsProperty, ref _items, value); }
}
```

直接属性是样式属性的轻量级版本，支持以下功能：

* AvaloniaObject.GetValue
* AvaloniaObject.SetValue（仅适用于非只读属性）
* PropertyChanged
* Binding（仅使用 LocalValue 优先级）
* GetObservable
* AddOwner
* 元数据

它们不支持以下功能：

* 验证/强制（尽管可以在属性设置器中执行此操作）
* 覆盖默认值。
* 继承的值

### 在另一个类上使用 DirectProperty

与样式属性一样，您也可以在 DirectProperty 上调用 `AddOwner` 方法将其添加到另一个类中。因为直接属性引用控件上的字段，所以您还必须为该属性添加一个字段：

```csharp
public static readonly DirectProperty<MyControl, IEnumerable> ItemsProperty =
    ItemsControl.ItemsProperty.AddOwner<MyControl>(
        o => o.Items,
        (o, v) => o.Items = v);

private IEnumerable _items = new AvaloniaList<object>();

public IEnumerable Items
{
    get { return _items; }
    set { SetAndRaise(ItemsProperty, ref _items, value); }
}
```

### 何时使用 DirectProperty 和 StyledProperty

通常情况下，您应该将属性声明为样式属性。但是，直接属性有其优缺点：

优点：

* 对于每个实例，不需要为该属性分配额外的对象
* 属性 getter 是标准的 C# 属性 getter
* 属性 setter 是标准的 C# 属性 setter，可以引发事件。
* 您可以添加数据验证支持

缺点：

* 无法从父控件继承值
* 无法利用 Avalonia 的样式系统
* 属性值是字段，因此无论该属性是否在对象上设置，都会被分配

因此，在具有以下要求的情况下使用直接属性：

* 属性不需要进行样式化
* 属性通常或始终具有值

### 数据验证支持

如果您想允许属性验证数据并显示验证错误消息，则该属性必须作为 `DirectProperty` 实现，并且必须启用验证支持（`enableDataValidation: true`）。

**具有启用了数据验证的属性的示例**

```cs
public static readonly DirectProperty<MyControl, int> ValueProperty =
    AvaloniaProperty.RegisterDirect<MyControl, int>(
        nameof(Value),
        o => o.Value,
        (o, v) => o.Value = v, 
        enableDataValidation: true);
```

如果要 [重用另一个类的直接属性](broken-reference)，也可以启用数据验证。在这种情况下，请使用 `AddOwnerWithDataValidation`。

**示例：TextBox.TextProperty 属性重用 TextBlock.TextProperty，但添加了验证支持**

```cs
public static readonly DirectProperty<TextBox, string?> TextProperty =
    TextBlock.TextProperty.AddOwnerWithDataValidation<TextBox>(
        o => o.Text,
        (o, v) => o.Text = v,
        defaultBindingMode: BindingMode.TwoWay,
        enableDataValidation: true);
```
