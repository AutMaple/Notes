# Fish Shell

## 配置文件

- fish 在启动时会去加载 `~/.config/fish/config.fish` 文件中的配置
- `~/.cofig/fish/conf.d/` 文件夹下的配置文件优先于 `config.fish` 配置文件执行

## 重命名

在 fish 中一个命令对应一个 function, 当我们使用命令 `ll` 时，fish 首先会到 `~/.config/fish/functions/` 文件夹下去找 `ll.fish` 文件，没有时会到 `~/.config/fish/config.fish` 中去找到名为 `ll` 的 function，如果还没有就会到 `$PATH` 中配置的路径中去查找

官方推荐将自定义的命令放在 `~/.config/fish/functions/` 文件夹下，一个命令一个 fish 文件，命名的规则是：`<CommandName>.fish`, 如自定义的 ll 命令的文件名应该为 `ll.fish`，里面的内容如下：

```sh
function ll
	ls -hl $argv
end
```

## 自定义

自定义命令提示符可以使用 `fish_prompt` 函数

```sh
function fish_prompt
    set_color purple
    date "+%m/%d/%y"
    set_color F00
    echo (pwd) '>' (set_color normal)
end
```

显示的结果如下

![image-20220328101226987](/home/autmaple/Documents/Notes/Attachment/image-20220328101226987.png)

## 快捷键

| 快捷键                     | 描述                             |
| -------------------------- | -------------------------------- |
| `arrow_right` / `Ctrl + f` | 补全 fish 显示的建议             |
| `alt + arrow_right`        | 补全 fish 显示的建议中的一个单词 |
|                            |                                  |

