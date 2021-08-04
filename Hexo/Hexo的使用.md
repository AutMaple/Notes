# Hexo的使用

## 初始化Hexo网站

执行如下命令进行初始化：

```shell
$ hexo init [foldername]
$ cd [foldername]
$ npm insatll
```

`hexo init [foldername]` 命令的作用是设置网站资源和配置文件存放的位置

如果使用该命令时，没有指定`foldername`，那么`hexo`将会把当前所在目录设置为网站的根目录

`init` 命令实际上执行了如下命令:

```SHELL
$ git clone hexo-starter
$ git clone hexo-theme-landscape
```

作用是将主题等文件下载到当前目录或者是指定的文件目录

执行上述命令之后，网站根目录的文件结构如下：

```PLAINTEXT
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

各文件的作用：

- `_config.yml`: 这是整个网站的配置文件
- `package.json`: hexo 的版本和依赖信息
- `scaffolds`: 存放创建页面和文章所需要的模板
- `source`: 存放网站资源
- `themes`: 存放网站主题, hexo 根据主题文件生成对应的静态文件

**[注]** `source`文件夹中,除了`_posts`文件夹，其余任何以`_`开头的文件或者文件夹或者是隐藏文件都会被忽略

## 配置文件

[_config.yml](https://hexo.io/zh-cn/docs/configuration) 的详细解释可以去官方网站进行查阅

`Hexo`官方建议将`_config.themename.ymle`(主题配置文件)和`_config.yml`(项目的配置文件) 分开进行配置

`Hexo`在最后生成配置文件时，会将主题配置文件和项目配置文件进行合并

各配置文件的优先级：主题配置文件 > 项目配置文件 > 主题目录下的配置文件

主题配置配件和项目配置文件位于项目的根目录下，其中每一个主题文件下还有对应的主题配置文件，但是该配置文件的优先级最低

## Hexo命令的使用

[Hexo命令](https://hexo.io/zh-cn/docs/commands) 的详细使用可以前往官方网站进行查阅

`Hexo`的所有命令如下：

| 命令       | 说明                           |
| ---------- | ------------------------------ |
| `init`     | 初始化建站                     |
| `new`      | 创建新页面或者文章             |
| `generate` | 生成静态文件                   |
| `publish`  | 发布草稿                       |
| `server`   | 启动本地服务器                 |
| `deploy`   | 部署网站                       |
| `render`   | 渲染文件                       |
| `migrate`  | 从其他博客系统迁移内容         |
| `clean`    | 清除缓存文件和已生成的静态文件 |
| `list`     | 列出网站的资料                 |
| `version`  | 显示 hexo 的版本               |

### new 命令

命令格式：`hexo new [layout] <title>`

- `layout`参数是可选参数，不填写将使用配置文件中设置的默认的模板进行创建
- `title`参数是必须进行的参数

#### layout布局

`layout`默认有三个可选值`post | page | draft`。new命令使用的`layout`参数不同，创建的文件存放的路径也不同

| 参数    | 路径             |
| ------- | ---------------- |
| `post`  | `source/_posts`  |
| `page`  | `source`         |
| `draft` | `source/_drafts` |

其中使用自定义布局创建的文件存放的位置和`post`参数存放的位置相同

## front-matter

`front-matter`是文件最上方被`---`包围的部分

### tags和categories

只有文章支持`tags`和`categories`,页面不支持这两个参数

在其他系统中，分类和标签听起来很接近，但是在 Hexo 中两者有着明显的差别：分类具有顺序性和层次性，
也就是说`Foo, Bar` 不等于 `Bar, Foo`；而标签没有顺序和层次。

```YAML
categories:
  - Diary
  - Life
```

上面的代码在 `hexo` 表示 `Life`是`Diary`的子类，而并非是同级关系

如果需要为文章添加多个分类可以使用如下方法

```YAML
categories:
  - [Diary, PlayStation]
  - [Diary, Games]
  - [Life]
```

此时这篇文章同时包括三个分类： `PlayStation` 和 `Games` 分别都是父分类 `Diary` 的子分类，同时 `Life` 是一个没有子分类的分类

## 标签插件

标签插件和 `Front-matter` 中的标签不同，它们是用于在文章中快速插入特定内容的插件

详细使用说明前往[官网](https://hexo.io/zh-cn/docs/tag-plugins)进行查阅

## 资源文件夹

资源表示的是`source`文件夹中除了文章以外的文件。如果Hexo项目中只有少量图片，那最简单的方法就是将它们放在 `source/images` 文件夹中。然后通过类似于 `![](/images/image.jpg)` 的方法访问它们。

如果想将资源文件夹与每篇文章相对应，可以在项目配置文件`_config.yml`中设置`post_asset_folder: true`,默认值为`false`

打开这个功能之后，每次创建新的文章时，都会在文章所在的目录中创建一个和`title`相同的文件夹，该文件夹用于放置对应文件的资源

此时通过常规的 markdown 语法和相对路径来引用图片和其它资源**可能**会导致它们在存档页或者主页上显示不正确

肯定能够正确显示相关的资源文件需要使用如下语法:

```PLAINTEXT
{% asset_path slug %} # 将在文章中输出生成静态文件之后对应文件的路径，该路径加上服务器地址之后可以直接访问对应的资源
{% asset_img slug [title] %} # 将在文章中展示对应路径的图片
{% asset_link slug [title] %} # 生成对应文件的一个超链接
```

例如显示资源文件夹中的图片

```MARKDOWN
![kobe bryant](imgs/kobe1.jpeg) # 也可以正常显示
{% asset_path imgs/kobe1.jpeg This is a Kobe image %} # 显示路径
{% asset_img imgs/kobe1.jpeg This is a Kobe image %} # 显示图片
{% asset_link imgs/kobe1.jpeg This is a Kobe image link%} # 显示图片超链接
```

## 数据文件的使用

[官网](https://hexo.io/zh-cn/docs/data-files)

## 自定义链接的生成方式

[官网](https://hexo.io/zh-cn/docs/permalinks)