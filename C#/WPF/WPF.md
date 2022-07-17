# WPF 简介

WPF 的功能是用来编写应用程序的表示层，至于业务层和数据层的开发，有专门的技术。比如业务层的技术；WCF(Windows Communication Foundation) 和 WF (Windows Workflow Foundation)

WF 的主要作用是设计工作流，而设计工作流的编程语言正是 WPF 总的界面设计语言：XAML, 也就是说学习完 WPF 之后，WF 也会了一半

WP F的布局理念就是把一个布局元素作为 ContentControl 或 HeaderedContentControl 族控件的 Content，再在布局元素里添加要被布局的子级控件，如果 UI 局部需要更复杂的布局，那就在这个区域放置一个子级的布局元素，形成布局元素的嵌套。

## 表示层应用程序

开发表示层应用程序不可避免的四个模块：

- 数据模型：现实世界中事物和逻辑的抽象
- 业务逻辑：数据模型之间的关系和交互
- 用户界面：由控件构成，与用户进行交互的界面，用于把数据展示给用户并响应用户的输入
- 界面逻辑：控件与控制之间的关系和交互

![image-20220628103719999](.\Attachment\image-20220628103719999.png)

## XAML

XAML(Extensible Application Markup Language) 是 WPF 技术中专门用于设计 UI 语言    

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

## WPF 的 UI 元素类型

| 名称                   | 描述                           |
| ---------------------- | ------------------------------ |
| ContentControl         | 单一内控件                     |
| headeredContentControl | 带标题的单一内容控件           |
| ItemsControl           | 以条目集合为内容的控件         |
| HeaderedItemsControl   | 带标题的以条目集合为内容的控件 |
| Decorator              | 控件装饰元素                   |
| Panel                  | 面板类元素                     |
| Adorder                | 文字点缀元素                   |
| Flow Text              | 流式文字元素                   |
| TextBox                | 文本输入框                     |
| TextBlock              | 静态文字                       |
| Shape                  | 图形元素                       |

## Decorator 族元素

| 元素             | 元素                   | 元素            | 元素                   |
| ---------------- | ---------------------- | --------------- | ---------------------- |
| ButtonChrome     | ClassicBorderDecorator | ListBoxChrome   | SystemDropShadowChrome |
| Border           | InkPresenter           | BulletDecorator | Viewbox                |
| AdornerDecorator |                        |                 |                        |

## Shape 族元素

友好的用户界面离不开各种图形的搭配，Shape 族元素（它们只是简单的视觉元素，不是控件）就是专门用来在 UI 上绘制图形的一类元素。这类元素没有自己的内容，我们可以使用 Fill 属性为它们设置填充效果，还可以使用 Stroke 属性为它们设置边线的效果。
本族元素的特点如下：

- 均派生自 Shape 类。
- 用于 2D 图形绘制。
- 无内容属性。
- 使用 Fill 属性设置填充，使用 Stroke 属性设置边线。

## Panel 族元素

所有用于 UI 布局的元素都属于这一族，该族控件非常的重要

本族元素的特点如下：

- 均派生自 Panel 抽象类。
- 主要功能是控制 UI 布局。
- 内容属性为 Children。
- 内容可以是多个元素，Panel元素将控制它们的布局。

### Grid 布局控件

对于 Grid 的行高和列宽，可以设置三种值：

- 绝对值： double + 单位
- 比例值: double + *。WPF 会自动根据对应的比例分配宽和高
- 自动值: Auto。根据元素的内容自动调整宽和高。分别却宽和高的最大值
