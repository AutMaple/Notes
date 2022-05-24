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

