# 快捷键

实现 `ICommandSource` 接口的各种控件都有一个 `HotKey` 属性，您可以将其设置或绑定。按下快捷键将会执行与控件绑定的命令。

```markup
<Menu>
    <MenuItem Header="_文件">
        <MenuItem x:Name="SaveMenuItem" Header="_保存" Command="{Binding SaveCommand}" HotKey="Ctrl+S"/>
    </MenuItem>
</Menu>
```

您也可以使用 `HotKeyManager` 类的静态方法从代码中设置和获取快捷键：

```csharp
InitializeComponent();

var saveMenuItem = this.FindControl<Avalonia.Controls.MenuItem>("SaveMenuItem");

HotKeyManager.SetHotKey(saveMenuItem, new KeyGesture(Key.S, KeyModifiers.Control));
```

> 有关使用 FindControl 方法查找命名控件的更多信息，请参见 [查找控件](https://docs.avaloniaui.net/guides/basics/code-behind#locating-controls) 部分。

## 键和修饰符

一个快捷键必须有一个 [Key](http://reference.avaloniaui.net/api/Avalonia.Input/Key/)，以及零个或多个 [KeyModifiers](http://reference.avaloniaui.net/api/Avalonia.Input/KeyModifiers/)。在使用 `HotKey` 属性在 XAML 中设置快捷键时，字符串将被解析为 [KeyGesture](http://reference.avaloniaui.net/api/Avalonia.Input/KeyGesture/)。解析键和修饰符使用 [Enum.Parse](https://docs.microsoft.com/en-us/dotnet/api/system.enum.parse)，但是可以使用诸如 `Ctrl` 而不是 `Control` 或 `Win` 而不是 `Meta` 之类的同义词。

### 参考资料 <a href="#reference" id="reference"></a>

* [HotKeyManager](http://reference.avaloniaui.net/api/Avalonia.Controls/HotKeyManager/)
* [KeyGesture](http://reference.avaloniaui.net/api/Avalonia.Input/KeyGesture/)
* [KeyModifiers](http://reference.avaloniaui.net/api/Avalonia.Input/KeyModifiers/)
* [Key](http://reference.avaloniaui.net/api/Avalonia.Input/Key/)

### 源代码 <a href="#source-code" id="source-code"></a>

* [HotkeyManager.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Controls/HotkeyManager.cs)
* [KeyGesture.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Input/KeyGesture.cs)
