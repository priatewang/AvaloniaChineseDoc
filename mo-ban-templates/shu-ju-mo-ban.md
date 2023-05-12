# 数据模板

许多控件都有一个 `Content` 属性，例如 [`ContentControl.Content`](http://reference.avaloniaui.net/api/Avalonia.Controls/ContentControl/4B02A756)。`Window` 继承自 [`ContentControl`](https://docs.avaloniaui.net/docs/controls/contentcontrol)，因此我们可以以此为例。您可能已经熟悉了将控件放在 `Window.Content` 属性中时会发生什么 - 窗口会显示该控件：

```
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
  <Button HorizontalAlignment="Center"
          VerticalAlignment="Center">
    Hello World!
  </Button>
</Window>
```

类似地，如果将字符串作为窗口内容，则窗口将显示该字符串：

```xaml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
  Hello World!
</Window>
```

但是，如果尝试将对象作为窗口内容显示，会发生什么？

```C#
namespace Example
{
    public class Student
    {
        public Student(string firstName, string lastName)
        {
            FirstName = firstName;
            LastName = lastName;
        }

        public string FirstName { get; }
        public string LastName { get; }
    }
}
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:Example">
  <local:Student FirstName="Jane" LastName="Deer"/>
</Window>
```

这样并不是很有用。这是因为 Avalonia 不知道如何显示 `Student` 类型的对象 - 因为它不是控件，所以会退回到对对象调用 `.ToString()` 方法。我们可以通过定义数据模板来告诉 Avalonia 如何显示非控件对象。

在 `Window`（以及任何从 `ContentControl` 继承的控件）上最简单的方法是设置 [`ContentTemplate`](http://reference.avaloniaui.net/api/Avalonia.Controls/ContentControl/7AA9343E) 属性：

```xaml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:Example">
  <Window.ContentTemplate>
    <DataTemplate>
      <StackPanel>
        <Grid ColumnDefinitions="Auto,Auto" RowDefinitions="Auto,Auto">
          <TextBlock Grid.Row="0" Grid.Column="0">First Name:</TextBlock>
          <TextBlock Grid.Row="0" Grid.Column="1" Text="{Binding FirstName}"/>
          <TextBlock Grid.Row="1" Grid.Column="0">Last Name:</TextBlock>
          <TextBlock Grid.Row="1" Grid.Column="1" Text="{Binding LastName}"/>
        </Grid>
      </StackPanel>
    </DataTemplate>
  </Window.ContentTemplate>

  <local:Student FirstName="Jane" LastName="Deer"/>
</Window>
```

窗口内容的数据模板不仅来自于 `ContentTemplate` 属性。每个控件还有一个 `DataTemplates` 集合，可以将任意数量的数据模板放置在其中。如果控件没有本地设置模板（例如在 `ContentTemplate` 中），则它将在其 `DataTemplates` 集合中查找匹配的模板。如果在其中找不到匹配项，则它将继续搜索其父级的 `DataTemplates`，然后是其祖先级别的，以此类推，直到达到 `Window`。如果仍然找不到匹配项，则它将在 `App.xaml`/`App.axaml` 中查找匹配的 `DataTemplate`，最后当所有这些选项都用完时，它将简单地对该对象调用 `.ToString()`。

`DataTemplate` 是按类型匹配的：模板匹配的类型是通过在模板上设置 `DataType` 属性指定的。

> 记住：在 `DataTemplates` 集合中的每个 `DataTemplate` 都应将其 `DataType` 设置为其所匹配的对象类型，否则数据模板将不会匹配任何内容！

使用 `DataTemplates` 集合，前面的示例可以编写为：

```xaml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:Example">
  <Window.DataTemplates>
    <DataTemplate DataType="{x:Type local:Student}">
      <Grid ColumnDefinitions="Auto,Auto" RowDefinitions="Auto,Auto">
        <TextBlock Grid.Row="0" Grid.Column="0">First Name:</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="1" Text="{Binding FirstName}"/>
        <TextBlock Grid.Row="1" Grid.Column="0">Last Name:</TextBlock>
        <TextBlock Grid.Row="1" Grid.Column="1" Text="{Binding LastName}"/>
      </Grid>
    </DataTemplate>
  </Window.DataTemplates>

  <local:Student FirstName="Jane" LastName="Deer"/>
</Window>
```

使用这种机制，如果您想在整个 `Window` 中重用 `DataTemplate`，则可以在 `Window.DataTemplates` 中指定它；如果您想要在整个应用程序中使用模板，则可以在 `App.xaml`/`App.axaml` 中的 `Application.DataTemplates` 集合中指定它。

现在让我们将另一个视图模型添加到混合中：

```C#
namespace Example
{
    public class Teacher
    {
        public Teacher(string firstName, string lastName)
        {
            FirstName = firstName;
            LastName = lastName;
        }

        public string FirstName { get; }
        public string LastName { get; }
    }
}
```

现在，我们可以为 `Teacher` 类型添加一个单独的数据模板，根据 `MainWindowViewModel.Content` 属性中对象的类型，显示适当的视图：

```Xaml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:Example">
  <Window.DataTemplates>

    <DataTemplate DataType="{x:Type local:Student}">
      <Grid ColumnDefinitions="Auto,Auto" RowDefinitions="Auto,Auto">
        <TextBlock Grid.Row="0" Grid.Column="0">First Name:</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="1" Text="{Binding FirstName}"/>
        <TextBlock Grid.Row="1" Grid.Column="0">Last Name:</TextBlock>
        <TextBlock Grid.Row="1" Grid.Column="1" Text="{Binding LastName}"/>
      </Grid>
    </DataTemplate>

    <DataTemplate DataType="{x:Type local:Teacher}">
      <Grid ColumnDefinitions="Auto,4,Auto">
        <TextBlock Grid.Row="0" Grid.Column="0">Professor</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="2" Text="{Binding LastName}"/>
      </Grid>
    </DataTemplate>

  </Window.DataTemplates>

  <ContentControl Content="{Binding Content}"/>
</Window>
```

### 评估顺序

Avalonia 中的数据模板可以针对接口和派生类，因此 DataTemplates 的顺序很重要：同一集合内的 DataTemplates 按声明顺序进行评估，因此您需要按照代码中的最具体到最不具体的顺序进行排列。

## 示例

[基本数据模板示例](https://github.com/AvaloniaUI/Avalonia.Samples/tree/main/src/Avalonia.Samples/DataTemplates/BasicDataTemplateSample)
