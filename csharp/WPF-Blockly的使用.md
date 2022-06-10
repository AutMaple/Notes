# WPF-Blockly 的使用

## 自定义 Expression 和 Statement

expression：指的是表达式，如 x++, x + y

```csharp
//定义表达式
//expression 中不能有 statement
public abstract class Expression : Node
{
    public abstract string ReturnType { get; }

    //描述步骤组成，用来绘制
    public abstract Descriptor Descriptor { get; }

    //ID used to store and load
    public abstract string Type { get; }
}
```

statement：指的是一行或者多行代码

```csharp
//定义程序步骤,单步可执行语句
public abstract class Statement: Node
{
    public abstract string ReturnType { get; }

    //描述步骤组成，用来绘制
    public abstract Descriptor Descriptor { get; }

    //ID used to store and load
    public abstract string Type { get; }
    public abstract BlockDescriptor BlockDescriptor { get; }
    public abstract bool IsClosing { get; }
}
```

The Descriptor for Expression

- TextItemDescriptor - text showed (IsKeyword property used to highlight the key word)
- ExpressionDescriptor - text box for user input or drag a expression here
- ParameterDescriptor - parameter of function, user can drag this variable to other location
- VariableDeclarationDescription - variable declaration, user can drag this variable to other location
- StringInputDesciptor - text box for user input a single line of text
- MultiLineStringInputDesciptor - text box for user input multiple line of text
- SelectionItemDescriptor - combo box to show multiple variable for user selection
- ImageItemDescriptor - show image in expression

 ExecutionEnvironment stores all the value of variable

