# Git的使用

## Git 远程仓库操作

##### Git global setup

```
git config --global user.name "郝永福"
git config --global user.email "haoyongfu@yihuosoft.com"
```

##### Create a new repository

```
git clone git@git.local.com:rp-cilent/rp-processedit.git
cd rp-processedit
touch README.md
git add README.md
git commit -m "add README"
```

##### Push an existing folder

```
cd existing_folder
git init
git remote add origin git@git.local.com:rp-cilent/rp-processedit.git
git add .
git commit -m "Initial commit"
```

##### Push an existing Git repository

```
cd existing_repo
git remote rename origin old-origin
git remote add origin git@git.local.com:rp-cilent/rp-processedit.git
```

## Git 的一些概念

- 工作区: 平常看到的目录
- 暂存区：`.git/index` 文件夹下的内容
- 版本库: `.git` 文件夹

![img](../Attachment/1352126739_7909.jpg)

说明：

- `HEAD` 表示是分支，如 master，main，... 可以是任何名字
- `objects` 标识的区域是 Git 的对象库，内容存放在 `.git/objects` 目录中

工作流程：

- 当对工作区修改（或新增）的文件执行 `git add` 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到 Objects 对象库中的一个新的对象中，而该对象的 ID 被记录在暂存区的文件索引中。
- 当执行 `git commit` 操作时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 的目录树会替换成暂存区中的目录树
- 当执行 `git reset HEAD` 命令时，暂存区的目录树被 master 分支指向的目录树所替换，但是工作区不受影响。
- 当执行 `git rm --cached <file>` 命令时，会直接从暂存区删除文件，工作区则不做出改变。
- 当执行 `git checkout .` 或者 `git checkout -- <file>` 命令时，会用暂存区全部或指定的文件替换工作区的文件。**这个操作很危险，会清除工作区中未添加到暂存区中的改动。**
- 当执行 `git checkout HEAD .` 或者 `git checkout HEAD <file>` 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。**这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。**

## 从命令行创建一个仓库

```bash
echo "# Notes" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:AutMaple/Notes.git
git push -u origin main
```

## 推送内容到一个仓库

```bash
git remote add origin git@github.com:AutMaple/Notes.git
git branch -M main
git push -u origin main
```

## Git 配置信息

```bash
git config --list # 列出当前仓库的所有配置
git config -e # 使用 vim 编辑器编写当前 git 仓库的配置
git config -e --global # 使用 vim 编辑器编写 git 的全局配置

# 配置全局用户信息
git config --global user.name "runoob"
git config --global user.email test@runoob.com
```

## Git 提交与修改

```bash
git diff # 查看工作区和暂存区 <file> 文件的差异
git reset # 回退版本，用 HEAD 分支中的数据替换暂存区中的数据
git rm # 删除工作区中的文件
git mv # 移动或重命名工作区中的文件
```

### Diff 命令

- 显示暂存区和工作区的差异

```bash
git diff <file>
```

- 显示暂存区和上一次提交的版本中的差异

```bash
git diff --cached <file>
git diff --staged <file>
```

上面两个命令都可以实现

### 删除文件

```bash
git rm <file> -f # 删除工作区和暂存区中的文件
git rm <file> --cached # 删除暂存区中的文件，保留工作区中的文件
```

## Git 远程操作

```bash
git remote -v # 显示所有的远程仓库地址和别名
git remote show # 显示远程仓库地址的别名
git remote show <name> # 显示指定仓库的详细信息，name 可以通过 git remote show 进行查询
git remote add <name> <url> # 添加远程仓库地址，并为地址设置一个 name
git remote rm <name> # 删除 name 对应的远程仓库地址
git remote rename old_name new_name # 修改远程仓库地址的对应的名字
```

## pull 操作

```bash
# 拉取 name 对应远程仓库中的 remote_branch 分支与本地的 local_branch 分支进行合并
# 如果是与当前分支进行合并，则 : 号以及它后面的内容可以省略
git pull <name> <remote_branch>:<local_branch>
```

## 分支管理

```bash
git branch # 输出本地所有的分支，并且当前所在的分支在输出时前面有一个 * 号
git branch <name> # 创建分支
git branch -d <name> # 删除 name 分支
git checkout <name> # 切换分支, 切换分支时，会用最后一次提交的版本替换本地文件
git checkout -b <name> # 创建 name 分支，并切换到 name 分支
git merge <name> # 将 name 分支的内容合并到当前分支
```

## 版本管理 TAG

```bash
 # 为最新提交的版本打上标签, 方便后续的查找和切换, -a 表示创建一个带注解(annotation)的标签
git tag -a <name>
```

# 冲突管理

## 未 commit 先 pull

对于修改代码前忘记拉取代码而远程仓库中又有对应的修改时，视工作量的大小，选择 revert 和 stash

1. 如果本地修改量比较小，可以遵循如下流程:

```txt
revert(把自己的代码取消) => pull => 在最新的代码上进行修改
```

2. 如果本地修改量比加大，这可以线将本地修改进行隐藏，然后拉取远程代码

```txt
stash save(将自己的代码隐藏) => pull => stash pop(将隐藏的代码重新拉回来) => merge (会有冲突，得手动进行解决)
```

