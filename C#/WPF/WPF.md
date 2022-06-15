# WPF 项目结构图

```text
HelloWPF/
	Core/
	Fonts/
	Images/
	MVVM/
		Views/
		Models/
		ViewAndModels/
	Theme/
```

- Core 文件夹下用于存放一些共用的类，如 RelayCommand，ObservableObject 类等
- Fonts 文件夹用于存放字体资源
- Images 文件夹用于存放图片资源

- MVVM 文件夹表示项目采用 MVVM 的模式开发 WPF 应用程序。
  - Views 文件夹存放视图文件，专注于做视图，即 xaml 文件
  - Models 文件夹用于存放实体类，即 ORM，用于将表转换成 C# 中的类
  - ViewAndModels 文件夹用于存放业务逻辑，每个类对应一个视图，提供如数据绑定的数据，Command 等
- Theme 文件夹用于存放自定义的控件主题，自定义控件的样式

# Template

The ControlTemplate contains the tree of elements that define the desired look.

- XAML Code
- Logic Code
- WPF Resources

For WPF Resources, there are three parts of resource you can change

- Window.Resource
- ControlName.Resource
- Applicati on.Resource

Every predefined control in the WPF that has a visual appearance also has a template that entirely defines that appearance. **This template is an object of type ControlTemplate that is set to the Template property defined by the Control class**

A ControlTemplate specifies the visual structure and visual behavior of a control

# 自定义控件

WPF 提供了三种方式来让开发人员创建自定义控件，分别是 UserControl, Control, FrameworkElement

## 一、基于 UserControl

创建控件最简单一个方法就是基于 UserControl 类进行继承。此时，我们可以将 WPF 中现有组件添加到 UserControl 画布上来，并将各组件进行命名，这样可以在后台进行组件访问和使用事件处理程序。 UserControl 可以利用丰富内容、样式和触发器的优点。但是，继承自 UserContro 的控件，将无法使用 DataTemplate 或 ControlTemplate 来自定义 UI 外观。

## 二、基于 Control 

基于 Control 类创建自定义控件的方法，可以使用模板定义 UI 外观。而且**可以将后台逻辑和前端样式展现上进行分离**。 另外，这种方法创建的自定义控件，还支持使用**命令和绑定**来完成相关动作，实现类似事件的效果。最后，控件可以重新定义 ControlTemplate 和 DataTemplate 来自定义 UI 外观。控件支持不同的主题。

## 三、基于 FrameworkElement 

一般来说，基于 UserControl 或 Control 创建的自定义控件即可完成业务需求，但是，在一些特殊情况下，简单的元素组合不能满足自定义控件的 UI 外观要求。此时，基于 FrameworkElement 创建自定义控件是一个很好的选择。

基于 FrameworkElement 创建控件，一方面可以通过重写的 OnRender 方法进行 UI 的直接绘制。 另一方面，可以通过自定义元素组合来可视化编写组件的外观。

## 依赖属性

WPF 可以通过设置控件的属性来更改其外观和行为。其中的依赖属性可以让自定义控件执行以下操作：

- 在样式中设置该属性。
- 将该属性绑定到数据源。
- 使用动态资源作为该属性的值。
- 对该属性进行动画处理。

通过 DependencyProperty.Register() 方法将依赖属性注入到系统中，该方法包含如下如下几个参数：

- 属性的名称
- 属性的类型
- 拥有该属性的类型
- 属性元数据信息

```csharp
/// <summary>
/// Identifies the Value dependency property.
/// </summary>
public static readonly DependencyProperty ValueProperty =
    DependencyProperty.Register(
        "Value", typeof(decimal), typeof(NumericUpDown),
        new FrameworkPropertyMetadata(MinValue, new PropertyChangedCallback(OnValueChanged),
                                      new CoerceValueCallback(CoerceValue)));

/// <summary>
/// Gets or sets the value assigned to the control.
/// </summary>
public decimal Value
{
    get { return (decimal)GetValue(ValueProperty); }
    set { SetValue(ValueProperty, value); }
}
```

属性的名称通常会加上 Property 后缀，但是在使用时，不需要加上 Property 后缀，例如 ValuePropery 在 xaml 控件中使用时，只需要声明 Value 属性即可
