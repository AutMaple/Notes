# AppImage 文件

[AppImage](https://appimage.org/)是一种可执行文件格式，类似于Windows的exe文件，macOS的app文件，不过AppImage是运行在Linux上的可执行文件，而且是可以运行在不同发行版本的Linux，如Ubuntu, Debian, openSUSE, RHEL, CentOS, Fedora, Arch Linux ...

## 如何运行

运行AppImage文件非常简单，下载后，通过以下4个步骤即可

1. 打开一个终端(terminal)
2. 更换当前目录到AppImage所在的目录，`cd /path/to/AppImage`
3. 设置文件可以执行权限，`chmod +x my.AppImage`
4. 运行AppImage `./my.AppImage`

```
cd /path/to/AppImage
chmod +x my.AppImage
./my.AppImage
```

## **FUSE**

第一次执行的时候可能会碰到`PUSE`相关的问题

```
dlopen(): error loading libfuse.so.2

AppImages require FUSE to run.
You might still be able to extract the contents of this AppImage
if you run it with the --appimage-extract option.
See <https://github.com/AppImage/AppImageKit/wiki/FUSE>
for more information
```

此时在Arch Linux上需要安装`fuse2`

```
sudo pacman -S fuse2
```

更多关于FUSE的问题可以查看：[I get some errors related to something called “FUSE”](https://docs.appimage.org/user-guide/troubleshooting/fuse.html)

## **Desktop Entry**

总是需要通过终端来运行AppImage不免有限麻烦，此时我们可以创建[Desktop Entry](https://wiki.archlinux.org/title/desktop_entries)，集成到桌面的程序目录里。

Desktop Entries目录有

- `/usr/share/applications/`
- `/usr/local/share/applications/`
- `~/.local/share/applications/`

对于AppImage文件，我们可以把它的Desktop Entry放在`~/.local/share/applications/`，如下例子

```
# ~/.local/share/applications/my.desktop
[Desktop Entry]

# The type as listed above
Type=Application

# The version of the desktop entry specification to which this file complies
Version=1.0

# The name of the application
Name=jMemorize

# A comment which can/will be used as a tooltip
Comment=Flash card based learning tool

# The executable of the application, possibly with arguments.
Exec=/home/mike/AppImages/my.AppImage

# The name of the icon that will be used to display this entry
Icon=/home/mike/AppImages/icons/my.png

# Describes whether this application needs to be run in a terminal or not
Terminal=false

# Describes the categories in which this entry should be shown
Categories=Education;Languages;Java;
```

如果我们使用的是rofi，我们就可以通过下面的命令查找到新添加的AppImage应用

```
rofi -show drun
```

## 解压缩 AppImage 文件

其实AppImage是一个文件包，我们可以通过下面的命令，把里面文件提取出来 ，然后找到图标文件。更多查看：[Extract icon from appimage](https://askubuntu.com/questions/1115321/extract-icon-from-appimage)

```
chmod +x your.AppImage
./your.AppImage --appimage-extract
```