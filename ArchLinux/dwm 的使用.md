# dwm 的使用

## 安装必要的库

```shell
sudo pacman -S xorg-server xorg-init xorg-xrandr xorg-xsetroot chromium nitrogen 
```

## 文件说明

| 作用                  | 文件           |
| --------------------- | -------------- |
| 定义操作 dwm 的快捷键 | `config.def.h` |
|                       |                |
|                       |                |

## 配置 dwm

配置 dwm 是通过 config.h 来配置的，在 dwm 的源码中是没有 config.h 这个文件，需要将 config.def.h 复制一份到 config.h 中，然后在到 config.h 中进行配置

### config.def.h 中快捷键说明

| 快捷键                   | 作用                                                         |
| ------------------------ | ------------------------------------------------------------ |
| `Shift + Alt + Enter`    | 在屏幕中运行一个终端, 并将起放在 master 区域，其他的放在 stack 区域进行平铺 |
| `Shift + Alt + q`        | 退出 dwm                                                     |
| `Shift + Alt + C`        | 关闭一个窗口                                                 |
| `Alt + Enter`            | 将选中的终端放在 master 区域(放在屏幕的左边)                 |
| `Alt + J/K`              | 在屏幕打开的终端中进行移动                                   |
| `Alt + i`                | 增加 master 区域中的窗口数量(默认是一个窗口)                 |
| `Alt + D`                | 减少 master 区域中的窗口数量(默认是一个窗口)                 |
| `Alt + T`                | 切换到 `tiled` 模式( 平铺模式 )                              |
| `Alt + F`                | 切换到 floating 模式(浮动窗口模式)                           |
| `ALt + M`                | 切换到 monacle 模式(窗口最大化模式)                          |
| `Alt + P`                | 打开类似与 utools 的命令行工具 Dmenu，用于打开想要打开的文件, |
| `Shift + Alt + [number]` | 将选中的终端移动到 number 标签( tag )屏幕内                  |
| `Alt + 鼠标右键`         | 调整浮动窗口的大小                                           |
| `Alt + 鼠标左键`         | `移动浮动窗口`                                               |
| `Shift + Alt + Space`    | 将 master 窗口在浮动 floating 窗口模式和 tiled 平铺模式之间进行切换 |
|                          |                                                              |

### dwm 的三种布局模式

- tiled ==> 平铺模式，所有的窗口自动进行平铺
- floating ==> 浮动窗口模式，类似于 windows 操作系统的窗口管理模式
- monocle ==> 窗口最大化模式，该模式下，一个屏幕中只有一个窗口

## 启动 dwm

```shell
startx
```

## 状态栏设置

状态栏的显示可以使用 dwm 官网中提供的 slstatus 工具

## 补丁

- 系统托盘补丁：systray，可要可不要
