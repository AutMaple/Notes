界面上的每一个控件都继承自 Control 类 ，如果一个控件重复出现多次，并且需要多次判断，此时可以通过 Control 类来进行遍历，例如 CheckBox

```csharp
private void button1_Click(object sender, EventArgs e)
{
    string msg = "";
    foreach(Control c in Controls)
    {
        //判断控件是否为复选框控件
        if(c is CheckBox)
        {
            if (((CheckBox)c).Checked)
            {
                msg = msg + " " + ((CheckBox)c).Text;
            }
        }
    }
    if(msg != "")
    {
        MessageBox.Show("您选择的爱好是：" + msg, "提示");
    }
    else
    {
        MessageBox.Show("您没有选择爱好", "提示");
    }
}
```

# WinForm 窗体常用的属性

| 属性                  | 作用                                                         |
| --------------------- | ------------------------------------------------------------ |
| Name                  | 用来获取或设置窗体的名称                                     |
| WindowState           | 获取或设置窗体的窗口状态，取值有 3 种，即Normal（正常）、Minimized（最小化）、Maximized（最大化），默认为 Normal，即正常显示 |
| StartPosition         | 获取或设置窗体运行时的起始位置，取值有 5 种，即 Manual（窗体位置由 Location 属性决定）、CenterScreen（屏幕居中）、WindowsDefaultLocation（ Windows 默认位置）、WindowsDefaultBounds（Windows 默认位置，边界由 Windows 决定）、CenterParent（在父窗体中居中），默认为 WindowsDefaultLocation |
| Text                  | 获取或设置窗口标题栏中的文字                                 |
| MaximizeBox           | 获取或设置窗体标题栏右上角是否有最大化按钮，默认为 True      |
| MinimizeBox           | 获取或设置窗体标题栏右上角是否有最小化按钮，默认为 True      |
| BackColor             | 获取或设置窗体的背景色                                       |
| BackgroundImage       | 获取或设置窗体的背景图像                                     |
| BackgroundImageLayout | 获取或设置图像布局，取值有 5 种，即 None（图片居左显示）、Tile（图像重复，默认值）、Stretch（拉伸）、Center（居中）、Zoom（按比例放大到合适大小） |
| Enabled               | 获取或设置窗体是否可用                                       |
| Font                  | 获取或设置窗体上文字的字体                                   |
| ForeColor             | 获取或设置窗体上文字的颜色                                   |
| Icon                  | 获取或设置窗体上显示的图标                                   |

# WinForm  窗体常用的事件

| 事件             | 作用                                     |
| ---------------- | ---------------------------------------- |
| Load             | 窗体加载事件，在运行窗体时即可执行该事件 |
| MouseClick       | 鼠标单击事件                             |
| MouseDoubleClick | 鼠标双击事件                             |
| MouseMove        | 鼠标移动事件                             |
| KeyDown          | 键盘按下事件                             |
| KeyUp            | 键盘释放事件                             |
| FormClosing      | 窗体关闭事件，关闭窗体时发生             |
| FormClosed       | 窗体关闭事件，关闭窗体后发生             |

# System.Windows.Form 常用的方法

| 方法                      | 作用                     |
| ------------------------- | ------------------------ |
| void Show()               | 显示窗体                 |
| void Hide()               | 隐藏窗体                 |
| DialogResult ShowDialog() | 以对话框模式显示窗体     |
| void CenterToParent()     | 使窗体在父窗体边界内居中 |
| void CenterToScreen()     | 使窗体在当前屏幕上居中   |
| void Activate()           | 激活窗体并给予它焦点     |
| void Close()              | 关闭窗体                 |

自定义的窗体只需要继承 `System.Windows.Form` 类即可

# 消息框：MessageBox

消息框的展示需要调用静态方法：Show()

| 方法                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| DialogResult Show(string text)                               | 指定消息框中显示的文本（text）                               |
| DialogResult Show(string text, string caption)               | 指定消息框中显示的文本（text）以及消息框的标题（caption）    |
| DialogResult Show(string text, string caption, MessageBoxButtons buttons) | 指定消息框中显示的文本（text）、消息框的 标题（caption）以及消息框中显示的按钮 （buttons） |
| DialogResult Show(string text, string caption, MessageBoxButtons buttons, MessageBoxIcon icon) | 指定消息框中显示的文本（text）、消息框的 标题（caption ）、消息框中显示的按钮 （buttons）以及消息框中显示的图标（icon） |

MessageBoxButtons 枚举类型主要用于设置消息框中显示的按钮，具体的枚举值如下。

- OK：在消息框中显示“确定”按钮。
- OKCancel：在消息框中显示“确定”和“取消”按钮。
- AbortRetryIgnore：在消息框中显示“中止” “重试”和“忽略”按钮。
- YesNoCancel：在消息框中显示“是” “否”和“取消”按钮。
- YesNo：在消息框中显示“是”和“否”按钮。
- RetryCancel：在消息框中显示“重试”和“取消”按钮。


MessageBoxIcon 枚举类型主要用于设置消息框中显示的图标，具体的枚举值如下。

- None：在消息框中不显示任何图标。
- Hand、Stop、Error：在消息框中显示由一个红色背景的圆圈及其中的白色 X 组成的图标。
- Question：在消息框中显示由圆圈和其中的一个问号组成的图标。
- Exclamation、Warning：在消息框中显示由一个黄色背景的三角形及其中的一个感叹号组成的图标。
- Asterisk、Information：在消息框中显示由一个圆圈及其中的小写字母 i 组成的图标。

调用 MessageBox 类中的 Show 方法将返回一个 DialogResult 类型的值。

DialogResult 也是一个枚举类型，是消息框的返回值，通过单击消息框中不同的按钮得到不同的消息框返回值。

DialogResult 枚举类型的具体值如下。

- None：消息框没有返回值，表明有消息框继续运行。
- OK：消息框的返回值是 0K （通常从标签为“确定”的按钮发送）。
- Cancel：消息框的返回值是 Cancel （通常从标签为“取消”的按钮发送）。
- Abort：消息框的返回值是 Abort （通常从标签为“中止”的按钮发送）。
- Retry：消息框的返回值是 Retry （通常从标签为“重试”的按钮发送）。
- Ignore：消息框的返回值是 Ignore （通常从标签为“忽略“的按钮发送）。
- Yes：消息框的返回值是 Yes （通常从标签为“是“的按钮发送）。
- No：消息框的返回值是 No （通常从标签为“否“的按钮发送）。

# CheckedListBox 的使用

```csharp
public partial class CheckedListBox : Form
{
    public CheckedListBox()
    {
        InitializeComponent();
    }
    //“购买”按钮的点击事件，用于在消息框中显示购买的水果种类
    private void button1_Click(object sender, EventArgs e)
    {
        string msg = "";
        for(int i = 0; i < checkedListBox1.CheckedItems.Count; i++)
        {
            msg = msg + " " + checkedListBox1.CheckedItems[i].ToString();
        }
        if (msg != "")
        {
            MessageBox.Show("您购买的水果有：" + msg, "提示");
        }
        else
        {
            MessageBox.Show("您没有选购水果！", "提示");
        }
    }
}
```

