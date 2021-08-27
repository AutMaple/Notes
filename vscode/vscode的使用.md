# vscode的使用

## 常用快捷键

vscode 默认快捷键

| 快捷键                                       | 作用                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| Ctrl + K + S                                 | 打开快捷键面板                                               |
| Ctrl + Shift + 上下键  / Shift+ Alt + 上下键 | 多光标编辑                                                   |
| Ctrl + Shift + L                             | 自动搜索光标所在单词，并进入多光标模式，且光标位于所有匹配单词的结尾 |
| Ctrl + D                                     | 光标移动到单词结尾，并进入多光标模式，再次按下该快捷键，将会在下一个匹配的单词处添加一个光标 |
| Ctrl + /                                     | 添加单行注释                                                 |
| Alt + O                                      | 在头文件和源文件中切换                                       |
| Ctrl + Shift + J                             | 光标跳转到上次光标所在处                                     |
| Shift + 左右键                               | 向左或向右扩展一个字符选择的区域                             |
| Ctrl + Shift + 左右键                        | 向左或向右扩展一个单词选择的区域                             |
| Ctrl + Alt + C                               | 复制光标所在文件的全路径                                     |
| Ctrl + Shift + \                             | 自动跳转到匹配的大括号                                       |
| F12                                          | 前往函数的定义                                               |
| Ctrl + Shift + Enter                         | 向上插入一行                                                 |
| Ctrl + Enter                                 | 向下插入一行                                                 |
| Alt + 上下键                                 | 上下移动当前行                                               |

IDEA 快捷键

| 快捷键                                       | 作用                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| Ctrl + K + S                                 | 打开快捷键面板                                               |
| Ctrl + Shift + 上下键  / Shift+ Alt + 上下键 | 将光标所在行进行上下移动                                     |
| Ctrl + Shift + L                             | 自动搜索光标所在单词，并进入多光标模式，且光标位于所有匹配单词的结尾 |
| Ctrl + D                                     | 复制当前行并粘贴在光标的下一行                               |
| Ctrl + Y                                     | 删除当前行                                                   |
| Ctrl + /                                     | 添加单行注释                                                 |
| Alt + O                                      | 在头文件和源文件中切换                                       |
| Shift + F6                                   | 重命名当前作用域内所有与光标处单词匹配的单词                 |
| Shift + 左右键                               | 向左或向右扩展一个字符选择的区域                             |
| Ctrl + Shift + 左右键                        | 向左或向右扩展一个单词选择的区域                             |
| Ctrl + Shift + C                             | 复制光标所在文件的全路径                                     |
| Ctrl + Alt + L                               | 格式化文档                                                   |
| Ctrl + Alt + Enter                           | 向上插入一行                                                 |
| Shift + Enter                                | 向下插入一行                                                 |



## 配置文件

vscode 的配置可以在 UI 界面中进行设置，也可以在 settings.json 文件中进行设置

通过 settings.json 配置时，首先需要打开 settings.json 文件。该文件可以通过 `shift + ctrl + p` 打开命令面板，然后输入 `setting.json`来打开

常用的配置

```json
{
    // 主题
    "workbench.colorTheme": "Andromeda",
    // 字体
    "editor.fontFamily": "'SauceCodePro Nerd Font Mono'",
    // 字体大小
    "editor.fontSize": 14,
    "editor.fontLigatures": false,
    "C_Cpp.addNodeAddonIncludePaths": true,
    "code-runner.runInTerminal": true,
    "code-runner.saveAllFilesBeforeRun": true,
    "code-runner.saveFileBeforeRun": true,
    "debug.onTaskErrors": "abort",
    "workbench.iconTheme": "material-icon-theme",
    
    // vscode 窗口放大的倍数
   	// 窗口字体太小时可以进行设置
    "window.zoomLevel": 2.0
}
```



## 插件推荐

### 通用插件

| 插件名                    | 作用                     |
| ------------------------- | ------------------------ |
| Andromeda                 | 代码主题插件             |
| Code Runner               | 用于运行各编程语言的代码 |
| Material Icon Theme       | 文件图标插件             |
| IntelliJ IDEA Keybindings | IDEA 快捷键              |
|                           |                          |
|                           |                          |

### 各编程语言环境配置

#### C/C++

| 插件名           | 作用                                                |
| ---------------- | --------------------------------------------------- |
| C/C++            | C/C++ IntelliSense, debugging, and code browsing.   |
| C++ Intellisense | C/C++ Intellisense with the help of GNU Global tags |



## 常用插件的配置

### code runner

当点击运行按钮后，code runner 会在 .vscode 文件夹中生成一个两个 json 文件 ==> launch.json 和 task.json 。这两个配置文件控制如何编译和运行代码。

例如 c++ 中的 launch.json

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++",
            "type": "cppdbg",
            "request": "launch",
            // 执行该目录下对应的可执行文件
            "program": "${fileDirname}/out/${fileBasenameNoExtension}", 
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C/C++: g++ 生成活动文件",
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```

task.json

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++ 生成活动文件",
            "command": "/usr/bin/g++",
            // 命令行对应的参数
            "args": [
                "-g",
                "${file}",
                "${workspaceRoot}/src/utils/cpp/tree.cpp",
                "${workspaceRoot}/src/111_minDepth.cpp",
                "-o",
                "${fileDirname}/out/${fileBasenameNoExtension}"
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

##### 配置文件中常用的变量

| 变量                    | 作用                                               |
| ----------------------- | -------------------------------------------------- |
| file                    | 表示点击运行按钮时正在编辑的文件的全路径           |
| workspaceRoot           | 项目根路径                                         |
| fileDirname             | 当前所编辑的文件所在的目录的全路径                 |
| fileBasenameNoExtension | 表示当前所编辑的文件的文件名去掉文件类型之后的部分 |
