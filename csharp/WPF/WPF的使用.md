# 文档

- [Controls](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/controls/?view=netframeworkdesktop-4.8&viewFallbackFrom=netdesktop-6.0)
- [控件的使用](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/overview/?view=netdesktop-6.0#wpf-controls-by-function)

# 项目文件说明

App.xaml: 该文件是 Application 的 starting point。 .NET will go to this class for starting instructions and then start the desired Window or Page from there. One of the most commonly used features of the App.xaml file is to define global resources that may be used and accessed from all over an application, for instance global styles. 

```xml
<Application x:Class="WpfTutorialSamples.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
    <Application.Resources>

    </Application.Resources>
</Application>
```

StartupUri: 告诉 application 在启动时加载哪一个 Window or Page

you can subscribe to the **Startup event**, where you can manually create your starting window.

```xml
<Application x:Class="WpfTutorialSamples.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
			 Startup="Application_Startup">
    <Application.Resources></Application.Resources>
</Application>
```

注意：StartupUri 属性被替换成了 Startup 事件，我们可以在该事件对应的方法中自定义 application 启动的逻辑，如

```csharp
using System;
using System.Collections.Generic;
using System.Windows;

namespace WpfTutorialSamples
{
	public partial class App : Application
	{

		private void Application_Startup(object sender, StartupEventArgs e)
		{
			// Create the startup window
			MainWindow wnd = new MainWindow();
			// Do stuff here, e.g. to the window
			wnd.Title = "Something else";
			// Show the window
			wnd.Show();
		}
	}
}
```

## 命令行参数

命令行参数会通过 Startup 事件传递过来。可以通过 `e.Args` 获取命令行的参数

# 自定义启动的窗体

```xml
<Application x:Class="WpfTutorialSamples.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
    <Application.Resources>

    </Application.Resources>
</Application>
```

首先将 StartupUri 去掉，然后替换成 StartUp 事件

```xml
<Application x:Class="WpfTutorialSamples.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
			 Startup="Application_Startup">
    <Application.Resources></Application.Resources>
</Application>
```

之后在 code-behind 中设置自己启动项目时展示的窗口

```csharp
using System;
using System.Collections.Generic;
using System.Windows;

namespace WpfTutorialSamples
{
	public partial class App : Application
	{

		private void Application_Startup(object sender, StartupEventArgs e)
		{
			// Create the startup window
			MainWindow wnd = new MainWindow();
			// Do stuff here, e.g. to the window
			wnd.Title = "Something else";
			// Show the window
			wnd.Show();
		}
	}
}
```

# Margin 值的顺序

 left, top, right, bottom

```xml
<StackPanel Grid.Row="1" Grid.Column="1" Margin="5,0,0,0">
</StackPanel>
```

如果只提供一个值，那么 left, top, right, bottom 都使用相同的值

# Data Binding

官方文档：https://docs.microsoft.com/en-us/dotnet/desktop/wpf/data/?view=netdesktop-6.0

[Data Source View](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/data/binding-sources-overview?view=netdesktop-6.0)

 Only types derived from [DependencyObject](https://docs.microsoft.com/en-us/dotnet/api/system.windows.dependencyobject) can define dependency properties. All [UIElement](https://docs.microsoft.com/en-us/dotnet/api/system.windows.uielement) types derive from `DependencyObject`.

The binding engine uses CLR(common language runtime) reflection to get the values of the properties

When data binding is declared on XAML elements, they resolve data binding by looking at their immediate [DataContext](https://docs.microsoft.com/en-us/dotnet/api/system.windows.frameworkelement.datacontext) property. 

[Data Context](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/data/?view=netdesktop-6.0#data-context)

If the `DataContext` property for the object hosting the binding isn't set, the parent element's `DataContext` property is checked, and so on, up until the root of the XAML object tree. 

Use the [Mode](https://docs.microsoft.com/en-us/dotnet/api/system.windows.data.binding.mode?view=windowsdesktop-6.0) property to specify the direction of the data flow

[Binding Markup Extension](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/binding-markup-extension?view=netframeworkdesktop-4.8)

```xml
<object property="{Binding}" .../>  
-or-  
<object property="{Binding  bindProp1=value1[, bindPropN=valueN]*}" ...  
/>  
-or-  
<object property="{Binding path}" .../>  
-or  
<object property="{Binding path[, bindPropN=valueN]*}" .../>
```



# Button 

## Event

- Hover: the first button changes colors when the user hovers with the mouse over the button.
- Press: the second button requires that the mouse be pressed while the mouse pointer is over the button.
- Release: the third does not reset the background color of the buttons until the mouse is pressed and released on the button.

```xml
<Button Name="btn1" Background="Pink" 
        BorderBrush="Black" BorderThickness="1" 
        Click="OnClick1" ClickMode="Hover">
  ClickMe1
</Button>

<Button Name="btn2" Background="LightBlue" 
        BorderBrush="Black" BorderThickness="1" 
        Click="OnClick2" ClickMode="Press">
  ClickMe2
</Button>

<Button Name="btn3" 
        Click="OnClick3" ClickMode="Release">
  Reset
</Button>
```

# DataGrid

## Data Binding

To bind the [DataGrid](https://docs.microsoft.com/en-us/dotnet/api/system.windows.controls.datagrid?view=windowsdesktop-6.0) to data, set the [ItemsSource](https://docs.microsoft.com/en-us/dotnet/api/system.windows.controls.itemscontrol.itemssource?view=windowsdesktop-6.0) property to an [IEnumerable](https://docs.microsoft.com/en-us/dotnet/api/system.collections.ienumerable?view=windowsdesktop-6.0) implementation. Each row in the data grid is bound to an object in the data source, and each column in the data grid is bound to a property of the data object. In order for the [DataGrid](https://docs.microsoft.com/en-us/dotnet/api/system.windows.controls.datagrid?view=windowsdesktop-6.0) user interface to update automatically when items are added to or removed from the source data, the [DataGrid](https://docs.microsoft.com/en-us/dotnet/api/system.windows.controls.datagrid?view=windowsdesktop-6.0) must be bound to a collection that implements the [INotifyCollectionChanged](https://docs.microsoft.com/en-us/dotnet/api/system.collections.specialized.inotifycollectionchanged?view=windowsdesktop-6.0) interface, such as an [ObservableCollection](https://docs.microsoft.com/en-us/dotnet/api/system.collections.objectmodel.observablecollection-1?view=windowsdesktop-6.0). To automatically reflect property changes,   must implement the [INotifyPropertyChanged](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.inotifypropertychanged?view=windowsdesktop-6.0) interface. For more information, see [Data Binding (WPF)](https://docs.microsoft.com/en-us/dotnet/framework/wpf/data/data-binding-wpf).

# 国际化

与国际化相关的类：`CultureInfo`  

Applying another culture to your WPF application is quite easy. You will, potentially, be dealing with two attributes, found on the **CurrentThread** property of the **Thread** class: **CurrentCulture** and **CurrentUICulture**.

Applying another culture to your WPF application is quite easy. You will, potentially, be dealing with two attributes, found on the **CurrentThread** property of the **Thread** class: **CurrentCulture** and **CurrentUICulture**.

在 `Thread.CurrentThread` 属性中找到 `CurrentCulture` 和 `CurrentUICulture`

- `CurrentCulture` 用于控制数字，时间等数据的格式化，默认值是操作系统的语言
- `CurrentUICulture` 用于控制界面。This is only relevant if your application supports multiple languages. e.g. through the use of language-resource files.

**Notes**

Changing the culture during the **Application_Startup** event, or at the latest in the constructor of your main window, makes most sense, because values that are already generated aren't updated automatically when you change the **CurrentCulture** property. 

国际化的操作只有在界面加载的时候设置才会有意义，否则其他数据展示的方式将使用默认的值

使用到国际化需要使用到如下的 namespace

```csharp
using System.Threading;
using System.Globalization;
using System.Windows.Markup.Localizer
```

The UI elements and properties in your original XAML are extracted from the BAML form of XAML into key-value pairs by using the APIs under [System.Windows.Markup.Localizer](https://docs.microsoft.com/en-us/dotnet/api/system.windows.markup.localizer). Localizers use the key-value pairs to localize the application

## 多线程中的国际化

If your application uses more than one thread, you should consider using the **DefaultThreadCurrentCulture** property. It can be found on the CultureInfo class (introduced in .NET framework version 4.5) and will ensure that not only the current thread, but also future threads will use the same culture. You can use it like this, e.g. in the **Application_Startup** event:

```csharp
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE");
```

So, will you have to set both the **CurrentCulture** AND the **DefaultThreadCurrentCulture** properties? Actually, no - if you have not already changed the CurrentCulture property, setting the DefaultThreadCurrentCulture property will also be applied to the CurrentCulture property. In other words, **it makes sense to use the DefaultThreadCurrentCulture instead of CurrentCulture if you plan on using multiple threads in your application** - it will take care of all scenarios.

## 参考文章

- [https://wpf-tutorial.com/wpf-application/application-culture-uiculture/](https://wpf-tutorial.com/wpf-application/application-culture-uiculture/)
- [https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/wpf-globalization-and-localization-overview?view=netframeworkdesktop-4.8](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/wpf-globalization-and-localization-overview?view=netframeworkdesktop-4.8)
- [各语言对应的简写官网列表](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-lcid/a9eac961-e77d-41a6-90a5-ce1a8b0cdb9c)

# 控件

## TextBlock

*TextBlock is not a control, per se, since it doesn't inherit from the Control class, but it's used much like any other control in the WPF framework, so we'll call it a control to keep things simple.*

### 1. TextBlock 标签中支持的标签

the supported elements include `AnchoredBlock, Bold, Hyperlink, InlineUIContainer, Italic, LineBreak, Run, Span, and Underline`.

```xml
<Window x:Class="WpfTutorialSamples.Basic_controls.TextBlockInlineSample"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="TextBlockInlineSample" Height="100" Width="300">
    <Grid>
		<TextBlock Margin="10" TextWrapping="Wrap">
			TextBlock with <Bold>bold</Bold>, <Italic>italic</Italic> and <Underline>underlined</Underline> text.
		</TextBlock>
    </Grid>
</Window>
```

#### 1.1 HyperLink

```xml
<Window x:Class="WpfTutorialSamples.Basic_controls.TextBlockHyperlinkSample"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="TextBlockHyperlinkSample" Height="100" Width="300">
	<Grid>
		<TextBlock Margin="10" TextWrapping="Wrap">
			This text has a <Hyperlink RequestNavigate="Hyperlink_RequestNavigate" NavigateUri="https://www.google.com">link</Hyperlink> in it.
		</TextBlock>
	</Grid>
</Window>
```

#### 1.2 span

The Span element doesn't have any specific rendering by default, but allows you to set almost any kind of specific rendering, including font size, style and weight, background and foreground colors and so on. The great thing about the Span element is that it allows for other inline elements inside of it, making it easy to do even advanced combinations of text and style. In the following example, I have used many Span elements to show you some of the many possibilities when using inline Span elements:

```xml
<Window x:Class="WpfTutorialSamples.Basic_controls.TextBlockSpanSample"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="TextBlockSpanSample" Height="100" Width="300">
    <Grid>
		<TextBlock Margin="10" TextWrapping="Wrap">
			This <Span FontWeight="Bold">is</Span> a
			<Span Background="Silver" Foreground="Maroon">TextBlock</Span>
			with <Span TextDecorations="Underline">several</Span>
			<Span FontStyle="Italic">Span</Span> elements,
			<Span Foreground="Blue">
				using a <Bold>variety</Bold> of <Italic>styles</Italic>
			</Span>.
		</TextBlock>
	</Grid>
</Window>
```

## The Label control vs. the TextBlock control

So why use a Label at all then? Well, there are a few important differences between the Label and the TextBlock. The TextBlock only allows you to render a text string, while the Label also allows you to:

- Specify a border
- Render other controls, e.g. an image
- Use templated content through the ContentTemplate property
- **Use access keys to give focus to related controls**

The last bullet point is actually one of the main reasons for using a Label over the TextBlock control. Whenever you just want to render simple text, you should use the TextBlock control, since it's lighter and performs better than the Label in most cases.

## Multi-line TextBox

```xml
<Window x:Class="WpfTutorialSamples.Basic_controls.TextBoxSample"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="TextBoxSample" Height="160" Width="280">
    <Grid Margin="10">
		<TextBox AcceptsReturn="True" TextWrapping="Wrap" />
	</Grid>
</Window>
```

# 全局样式

```xml
<Window.Resources>
    <Style TargetType="{x:Type Button}">
    	<Setter Property="Padding" Value="5,2"/>
    </Style>
</Window.Resources>
```

## ToolTip

### ToolTipService

ToolTipService 类用于设置 ToolTip 的行为，可在官网中进行查询：[https://docs.microsoft.com/en-us/dotnet/api/system.windows.controls.tooltipservice?redirectedfrom=MSDN&view=windowsdesktop-6.0](https://docs.microsoft.com/en-us/dotnet/api/system.windows.controls.tooltipservice?redirectedfrom=MSDN&view=windowsdesktop-6.0)

```xml
<Button ToolTip="Create a new file" ToolTipService.ShowDuration="5000" Content="Open" />
```

##  文字渲染(text rendering)

## Controlling text rendering

With .NET framework 4.0, Microsoft also decided to give more control of text rendering to the programmer, by introducing the **TextOptions** class with the **TextFormattingMode** and **TextRenderingMode** properties. This allows you to specifically decide how text should be formatted and rendered on a control level. This is probably best illustrated with an example, so have a look at the code and the screenshots below to see how you can affect text rendering with these properties.

### TextFormattingMode

Using the TextFormattingMode property, you get to decide which algorithm should be used when formatting the text. You can choose between **Ideal** (the default value) and **Display**. You would normally want to leave this property untouched, since the Ideal setting will be best for most situations, but **in cases where you need to render very small text, the Display setting can sometimes yield a better result**. Here's an example where you can see the difference (although it's very subtle):

### TextRenderingMode

The **TextRenderingMode** property gives you control of which antialiasing algorithm is used when rendering text. It has the biggest effect in combination with the **Display** setting for the **TextFormattingMode** property, which we'll use in this example to illustrate the differences:

## Tab Order

通过 **TabIndex** and **IsTabStop** 属性控制 tab 键在控件之间的移动

# Access Keys

The concept of **Access Keys**, sometimes referred to as *Accelerator Keys* or *Keyboard Accelerators*, **allows you to reach a specific control inside a window by holding down the Alt key and then pressing another key on the keyboard.** This enhances the usability of your windows, because it allows the user to use their keyboard to navigate the window, instead of having to use the mouse.

# Panel

# Introduction to WPF panels

Panels are one of the most important control types of WPF. They act as containers for other controls and control the layout of your windows/pages. Since a window can only contain ONE child control, a panel is often used to divide up the space into areas, where each area can contain a control or another panel (which is also a control, of course).

Panels come in several different flavors, with each of them having its own way of dealing with layout and child controls. Picking the right panel is therefore essential to getting the behavior and layout you want, and especially in the start of your WPF career, this can be a difficult job. The next section will describe each of the panels shortly and give you an idea of when to use it. After that, move on to the next chapters, where each of the panels will be described in detail.

## Canvas

A simple panel, which mimics the WinForms way of doing things. It allows you to assign specific coordinates to each of the child controls, giving you total control of the layout. This is not very flexible though, because you have to manually move the child controls around and make sure that they align the way you want them to. Use it (only) when you want complete control of the child control positions.

## WrapPanel

The WrapPanel will position each of its child controls next to the other, horizontally (default) or vertically, until there is no more room, where it will wrap to the next line and then continue. Use it when you want a vertical or horizontal list controls that automatically wraps when there's no more room.

## StackPanel

The StackPanel acts much like the WrapPanel, but instead of wrapping if the child controls take up too much room, it simply expands itself, if possible. Just like with the WrapPanel, the orientation can be either horizontal or vertical, but instead of adjusting the width or height of the child controls based on the largest item, each item is stretched to take up the full width or height. Use the StackPanel when you want a list of controls that takes up all the available room, without wrapping.

## DockPanel

The DockPanel allows you to dock the child controls to the top, bottom, left or right. By default, the last control, if not given a specific dock position, will fill the remaining space. You can achieve the same with the Grid panel, but for the simpler situations, the DockPanel will be easier to use. Use the DockPanel whenever you need to dock one or several controls to one of the sides, like for dividing up the window into specific areas.

## Grid

The Grid is probably the most complex of the panel types. A Grid can contain multiple rows and columns. You define a height for each of the rows and a width for each of the columns, in either an absolute amount of pixels, in a percentage of the available space or as auto, where the row or column will automatically adjust its size depending on the content. Use the Grid when the other panels doesn't do the job, e.g. when you need multiple columns and often in combination with the other panels.

## UniformGrid

The UniformGrid is just like the Grid, with the possibility of multiple rows and columns, but with one important difference: All rows and columns will have the same size! Use this when you need the Grid behavior without the need to specify different sizes for the rows and columns.

## 数据日志级别

```xml
<TextBlock Text="{Binding Title, diag:PresentationTraceSources.TraceLevel=High}" />
```

该级别可以查看详细的数据绑定日志信息

# code-behind 中设置断点

```csharp
public class DebugDummyConverter : IValueConverter
	{
		public object Convert(object value, Type targetType, object parameter, System.Globalization.CultureInfo culture)
		{
			Debugger.Break();
			return value;
		}

		public object ConvertBack(object value, Type targetType, object parameter, System.Globalization.CultureInfo culture)
		{
			Debugger.Break();
			return value;
		}
	}
```

In the Code-behind file, we define a DebugDummyConverter. In the Convert() and ConvertBack() methods, we call Debugger.Break(), **which has the same effect as setting a breakpoint in Visual Studio**, and then return the value that was given to us untouched.

If the debugger never breaks, it means that the converter is not used. This usually indicates that you have an invalid binding expression, which can be diagnosed and fixed using the methods described in the start of this article. The dummy-converter trick is only for testing valid binding expressions.

# 模板

## Sytle VS Tmeplates

- Styles can only change the appearance of your control with default properties of that control.
- With templates, you can access more parts of a control than in styles. You can also specify both existing and new behavior of a control.

## Tempates Types

- **Control Template**：The Control Template defines the visual appearance of a control.
- **Data Template**：A Data Template defines and specifies the appearance and structure of a collection of data. It provides the flexibility to format and define the presentation of the data on any UI element. It is mostly used on data related Item controls such as ComboBox, ListBox, etc.

## Data Template

Data template is a bit of XAML that describes how bound data is displayed. A data template can contain elements that are each bound to a data property along with additional markup that describes layout, color and other appearance. **DataTemplate is, basically, used to specify the appearance of data displayed by a control not the appearance of the control itself.**

Therefore, DataTemplate can be applied to ContentControls or ItemsControl. The name of the property to which you can assign a DataTemplate depends whether the control is a content control or an ItemsControl.

The controls that can contain a *single item* (single logical child of type Object) are called “*content controls*”. These controls derive from the ContentControl base class. Controls that can contain a *collection of items* (many logical children of type Object) are called “*items controls*”.

It is recommended that data templates are defined in a resource collection and referenced in your element rather than defining them inline.

When binding a property or list directly to a control, you are limited to binding a single property. With data templates, however, you can bind more than one property in each item, thereby displaying multiple bits of related data together.

# Trigger 触发器

A trigger basically enables you to change property values or take actions based on the value of a property. So, it allows you to dynamically change the appearance and/or behavior of your control without having to create a new one.

Triggers are used to change the value of any given property, when certain conditions are satisfied. Triggers are usually defined in a style or in the root of a document which are applied to that specific control.

## Trigger Types

- Property Triggers: 当控件的某个属性符合时，it will bring either an immediate or an animated change in another property.
- Data Triggers：A data trigger performs some actions when the bound data satisfies some conditions
- Event Triggers: An event trigger performs some actions when a specific event is fired. It is usually used to accomplish some animation on control such DoubleAnumatio, ColorAnimation, 

