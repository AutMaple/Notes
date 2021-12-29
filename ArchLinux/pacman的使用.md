# pacman 的使用

## 常用命令

查询每个参数的子选项：`pacman -[options] --help`

| 命令                           | 作用                                                         | 示例 |
| ------------------------------ | ------------------------------------------------------------ | ---- |
| `pacman -Si [packageName]`     | 查看已安装软件的信息,如所属软件库，依赖。<br />如果不指定 `packageName`, 则将列出全部已安装软件的相关信息 |      |
| `pacman -Sg groupName`         | 查看哪些软件包属于 `groupName` 组                            |      |
| `pacman -Syyu`                 | 更新系统软件包                                               |      |
| `pacman -R packgeName`         | 删除 `packageName` 软件，但是保留它已经安装的全部依赖        |      |
| `pacman -Rs packageName`       | 删除 `packageName` 软件以及它所安装并且不被其他软件包依赖的软件包 |      |
| `pacman -Rsu packageName`      | 强制删除 `packageName` 软件以及它所安装并且不被其他软件包依赖的软件包 |      |
| `pacman -Rsc packageName`      | 删除 `packageName` 软件包和所有依赖这个软件包的程序,这个操作是递归的，谨慎使用 |      |
| `pacman -Rn packageName`       | 删除 `packageName` 软件包时，不备份重要配置文件              |      |
| `pacman -Q`                    | 查询本地软件包数据库                                         |      |
| `pacman -S`                    | 查询远程同步软件数据库                                       |      |
| `pacman -F`                    | 查询文件数据库                                               |      |
| `pacman -Qs string [string]`   | 查询已安装的软件，可使用正则表达式                           |      |
| `pacman -Si pacakgeName`       | 显示软件包的详细信息                                         |      |
| `pacman -Qi packageName`       | 显示安装包的详细信息                                         |      |
| `pacman -Qii packageName`      | 显示软件包的详细信息、备份文件和修改状态                     |      |
| `pacman -Ql packageName`       | 显示已安装软件包所包含的文件列表                             |      |
| `pacman -Fl packageName`       | 显示远程软件库中 `packageName` 软件包所包含的文件列表        |      |
| `pacman -Qk packageName`       | 检查已安装的软件包的安装文件是否都存在                       |      |
| `pacman -Qo /path/to/fileName` | 查询数据库获取某个文件属于哪个软件包                         |      |
| `pacman -F /path/to/fileName`  | 查询文件属于远程软件库中的那个软件包                         |      |
| `pacman -Qdt`                  | 显示所有不被依赖的软件包,即孤立的软件包                      |      |
| `pacman -U /path/to/package`   | 安装一个本地包                                               |      |
| `pacman -U https://.....`      | 安装一个远程包, 这个包不在 pacman 配置的源中                 |      |
| `pacman -Scc`                  | 清除安装包缓存                                               |      |
| `pacman -Rns $(pacman -Qtdq)`  | 清除孤立的包                                                 |      |

