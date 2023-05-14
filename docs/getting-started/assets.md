# 资源Assets

许多应用程序需要在其可执行文件中包含位图和[资源字典](https://docs.avaloniaui.net/docs/styling/resources)，并从XAML中引用这些资源。

### 包含资源

可以使用项目文件中的`<AvaloniaResource>`项将资源包含在应用程序中。默认情况下，MVVM应用程序模板将`Assets`目录中的所有文件都作为`<AvaloniaResource>`包含：

```XML
<ItemGroup>
  <AvaloniaResource Include="Assets\**"/>
</ItemGroup>
```

您可以通过添加其他`<AvaloniaResource>`元素来包含任何想要的文件。

您会注意到我们在这里提到了\_资源\_，而 MSBuild 项称为 Avalonia _资源_。内部将资源存储为[.NET资源](https://docs.microsoft.com/en-us/visualstudio/ide/managing-application-resources-dotnet)，但是因为术语“资源”与[XAML资源](https://docs.avaloniaui.net/docs/styling/resources)发生冲突，我们将在整个过程中将其称为“资源”。

### 引用资源

在 XAML 中引用资源时，可以使用相对路径：

```XML
<Image Source="icon.png"/>
<Image Source="images/icon.png"/>
<Image Source="../icon.png"/>
```

或者使用绝对路径：

```XML
<Image Source="/Assets/icon.png"/> 
```

如果资源位于不同的程序集中，可以使用 `avares:` URI 方案引用资源。例如，如果资源位于名为 `MyAssembly.dll` 的程序集中，可以使用：

```XML
<Image Source="avares://MyAssembly/Assets/icon.png"/>
```

对于字体，可以在 "#" 符号后面提供字体名称：

```XML
<TextBlock FontFamily="avares://MyAssembly/Assets/font.ttf#FontName">test</TextBlock>
```

Avalonia 提供了转换器，可以自动将资源 URI 转换为以下任何类型：[IImage](http://reference.avaloniaui.net/api/Avalonia.Media/IImage)、[IBitmap](http://reference.avaloniaui.net/api/Avalonia.Media.Imaging/IBitmap)、[WindowIcon](http://reference.avaloniaui.net/api/Avalonia.Controls/WindowIcon) 和 [FontFamily](http://reference.avaloniaui.net/api/Avalonia.Media/FontFamily)。

### 引用清单资源

还可以通过在项目文件中使用 `<EmbeddedResource>` MSBuild 项来将资源包含在 .NET 应用程序中，这将导致该文件作为 [清单资源](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.assembly.getmanifestresourcenames) 包含在程序集中。

可以使用 `resm:` URL 方案引用清单资源：

```XML
<Image Source="resm:MyApp.Assets.icon.png"/>
```

或者如果资源嵌入在与 XAML 文件不同的程序集中：

```XML
<Image Source="resm:MyApp.Assets.icon.png?assembly=MyAssembly"/>
```

资源的名称是由 MSBuild 自动从程序集名称、文件路径和文件名生成的，它们用点号分隔。如果 Avalonia 无法找到清单资源，请使用 [`Assembly.GetManifestResourceNames`](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.assembly.getmanifestresourcenames) 检查资源名称。

注意：从 11.0 开始，Avalonia 不再支持清单 "resm" 资源。请改用 Avalonia 资源 (avares)。

### 从代码中加载资源

可以使用 `IAssetLoader` 接口从代码中加载资源：

```C#
var assets = AvaloniaLocator.Current.GetService<IAssetLoader>();
var bitmap = new Bitmap(assets.Open(new Uri(uri)));
```

上面代码中的 `uri` 变量可以包含任何有效的 URI，包括上述 `avares:` 和 `resm:`。默认情况下，Avalonia 不支持 `file://`、`http://` 或 `https://` 方案。如果要从磁盘或网络加载文件，可以自己实现这个功能，或者使用社区实现，例如 https://github.com/AvaloniaUtils/AsyncImageLoader.Avalonia。
