# 剪贴板

Avalonia 通过 `IClipboard` 接口提供了对剪贴板的访问。你可以通过 `Application.Current.Clipboard` 获取当前应用程序的剪贴板实例。

```csharp
await Application.Current.Clipboard.SetTextAsync("Hello World!");

var text = await Application.Current.Clipboard.GetTextAsync();
```

你还可以在剪贴板中存储对象，但是在 Android 和 iOS 上不支持该功能。

```csharp
record Person(string Name, int Age);

var person = new Person("Peter Griffin", 58);

var dataObject = new DataObject();
dataObject.Set("my-app-person", person);

await Application.Current.Clipboard.SetDataObjectAsync(dataObject);

var storedPerson = (Person) await clipboard.GetDataAsync("my-app-person");
```

对象是使用格式字符串存储和检索的，该字符串应该是你的应用程序和对象类型的唯一标识。

### 参考 <a href="#reference" id="reference"></a>

[IClipboard](http://reference.avaloniaui.net/api/Avalonia.Input.Platform/IClipboard/)

### 源代码 <a href="#source-code" id="source-code"></a>

* [IClipboard.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Input/Platform/IClipboard.cs)
* [Avalonia.Win32/ClipboardImpl.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Windows/Avalonia.Win32/ClipboardImpl.cs)
* [Avalonia.X11/X11Clipboard.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.X11/X11Clipboard.cs)
* [Avalonia.Native/ClipboardImpl.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Avalonia.Native/ClipboardImpl.cs)
* [Avalonia.Android/Platform/ClipboardImpl.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/Android/Avalonia.Android/Platform/ClipboardImpl.cs)
* [Avalonia.iOS/ClipboardImpl.cs](https://github.com/AvaloniaUI/Avalonia/blob/master/src/iOS/Avalonia.iOS/ClipboardImpl.cs)
