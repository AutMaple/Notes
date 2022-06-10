# xaml 文件

# x:Name and Name

WPF framework-level XAML elements inherit a [Name](https://docs.microsoft.com/en-us/dotnet/api/system.windows.frameworkelement.name) property, which is equivalent to the XAML defined `x:Name` attribute. Certain other classes also provide property-level equivalents for `x:Name`, which is also usually defined as a `Name` property. Generally speaking, **if you can't find a `Name` property in the members table for your chosen element/type, use `x:Name` instead.** The `x:Name` values will provide an identifier to a XAML element that can be used at run-time, either by specific subsystems or by utility methods such as [FindName](https://docs.microsoft.com/en-us/dotnet/api/system.windows.frameworkelement.findname).

## 参考文章

- [xaml详细语法](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/xaml-syntax-in-detail?view=netdesktop-6.0)

- [Events and XAML code-behind](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/xaml/?view=netdesktop-6.0#events-and-xaml-code-behind)

- [WPF XAML Extensions](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/wpf-xaml-extensions?view=netframeworkdesktop-4.8&viewFallbackFrom=netdesktop-6.0): 该文章列举了扩展标记中可以使用的关键字，如 Binding, StaticResource, etc.

- [The x:prefix](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/xaml/?view=netdesktop-6.0#the-x-prefix)

## 注册 Resources

如果想把各种类型的资源分类管理起来，同时又要被其他的控件所使用，可以通过 `App.xaml` 实现，实现的例子如下

```xaml
<Application x:Class="WPFLocalization.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WPFLocalization"
             xmlns:viewModel="clr-namespace:WPFLocalization.MVVM.ModelAndView"
             xmlns:view="clr-namespace:WPFLocalization.MVVM.Views"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="Themes/MenuButtonTheme.xaml"/>
                <ResourceDictionary Source="Themes/TextBoxTheme.xaml"/>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```
