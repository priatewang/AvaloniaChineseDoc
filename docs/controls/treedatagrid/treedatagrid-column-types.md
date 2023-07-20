# TreeDataGrid 列类型

TreeDataGrid 目前支持三种不同的列类型：

* [TextColumn](https://github.com/AvaloniaUI/Avalonia.Controls.TreeDataGrid/blob/master/src/Avalonia.Controls.TreeDataGrid/Models/TreeDataGrid/TextColumn.cs)
* [HierarchicalExpanderColumn](https://github.com/AvaloniaUI/Avalonia.Controls.TreeDataGrid/blob/master/src/Avalonia.Controls.TreeDataGrid/Models/TreeDataGrid/HierarchicalExpanderColumn.cs)
* [TemplateColumn](https://github.com/AvaloniaUI/Avalonia.Controls.TreeDataGrid/blob/master/src/Avalonia.Controls.TreeDataGrid/Models/TreeDataGrid/TemplateColumn.cs)

### TextColumn

`TextColumn` 在您希望列中的所有单元格仅包含文本值时非常有用。通常，创建 `TextColumn` 类的实例所需的一切都是：

```csharp
new TextColumn<Person, string>("First Name", x => x.FirstName)
```

这里的第一个泛型参数实际上是您的模型类型，即您想要从中获取数据的地方。在这个例子中是 `Person`。第二个泛型参数是您要从中获取数据的属性的类型。在这种情况下，它是一个字符串，它将用于确定您的属性的确切类型。

![](https://user-images.githubusercontent.com/53405089/157456551-dd394781-903a-4c7b-8874-e631e21534a1.png)

这是 `TextColumn` 构造函数的签名。有两个最重要的参数。第一个将用于定义列标题，通常是一个字符串。在上面的示例中，它是 _"First Name"_。第二个参数是一个表达式，用于获取属性的值。在上面的示例中，它是 _x => x.FirstName_。

**注意**：\
上面的示例来自于[此文章](https://github.com/AvaloniaUI/Avalonia.Controls.TreeDataGrid/blob/master/docs/get-started-flat.md)。如果您需要更多示例，请随时查看该文章，其中有一个示例显示如何使用 TextColumn，并且如何使用它运行整个 `TreeDataGrid`。

### HierarchicalExpanderColumn

`HierarchicalExpanderColumn` 只能与 `HierarchicalTreeDataGrid`（即 TreeView）一起使用，这是其名称中 Hierarchical 的含义，同时它只能与 `HierarchicalTreeDataGridSource` 一起使用。当您希望单元格显示展开器以显示嵌套数据时，这种类型的列会很有用。

下面是实例化 `HierarchicalExpanderColumn` 类的方法：

```csharp
new HierarchicalExpanderColumn<Person>(new TextColumn<Person, string>("First Name", x => x.FirstName), x => x.Children)
```

`HierarchicalExpanderColumn` 只有一个泛型参数，即模型类型，与 `TextColumn` 中的一样，此处为 `Person`。

让我们看一下 `HierarchicalExpanderColumn` 构造函数。

![](https://user-images.githubusercontent.com/53405089/157536079-fd14f1ed-0a7d-438a-abba-fd56766709a9.png)

构造函数中的第一个参数是嵌套列，通常情况下，您可能希望在展开器之外显示一些内容，这就是为什么需要此参数。在上面的示例中，我们想要显示文本，因此我们使用了 `TextColumn`。第二个参数是子元素的选择器，即在展开器处于展开状态时在父元素下方显示的内容。

**注意**：\
上面的示例来自于[此文章](https://github.com/AvaloniaUI/Avalonia.Controls.TreeDataGrid/blob/master/docs/get-started-hierarchical.md)。如果您需要更多示例，请随时查看该文章，其中有一个示例显示如何使用 `HierarchicalExpanderColumn`，以及如何使用它运行整个 `TreeDataGrid`。

### TemplateColumn

TemplateColumn 是创建列的最可定制选项。您可以将基本上可以放入 `IDataTemplate` 中的任何内容放入此列单元格中。

下面是实例化 `TemplateColumn` 类的方法：

```csharp
new TemplateColumn<Person>(
    "Selected",
    new FuncDataTemplate<FileTreeNodeModel>((a, e) => new CheckBox()))
```

`TemplateColumn` 只有一个泛型参数，即模型类型，与 `TextColumn` 中的一样，此处为 `Person`。上面的代码将在每个单元格中创建一个带有标题为 _"Selected"_ 的列，并且在每个单元格中有一个 `CheckBox`。

![](https://user-images.githubusercontent.com/53405089/157664231-8653bce9-f8d6-4fbc-8e78-e3ff93f1ace2.png)

`TemplateColumn` 只有两个必需的参数。第一个参数是列标题，与其他地方相同。第二个是 `IDataTemplate`，实际上是一个模板，其中包含您要在该列的单元格中显示的内容。
