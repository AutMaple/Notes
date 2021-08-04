# Butterfly 的使用

[ButterFly官网](https://butterfly.js.org/)有详细的使用教程教程

## front-matter区域

`front-mattter` 区域就是每篇文章开头那段被`---`包围起来的区域，如：

```MARKDOWN
---
title: butterfly_usage
date: 2021-07-17 01:19:55
tags: hexo
---
```

这个区域用于设置文章的属性和展示的样式

`front-matter`分为两种：

- `Page front-matter`： 添加页面 ( page ) 时需要设置的参数。添加页面使用的命令是`hexo new page 页面名`
- `Post front-matter`： 添加文章 ( post ) 时需要设置的参数。添加文章使用的命令是`hexo new [模板名] 文章名`,其中的模板名可选，不写则使用配置文件中配置的默认模板生成对应的 markdown 文件

使用这两个命令生成文件的位置不一样

生成 `page` 时，生成的文件路径是：`source/页面名/index.md`

生成`post`时，生成的文件路径为`source/_posts/文章名.md`

### Page front-matter

`Page front-matter`的参数如下：

| 参数               | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| `title`            | [必填] 页面标题，归档时的名称默认也是使用`title`属性对应的值 |
| `date`             | [必填] 页面创建日期                                          |
| `type`             | [必填] 页面类型,其中`link`,`tags`,`categories`有特殊的作用   |
| `updated`          | [可选] 页面更新日期                                          |
| `description`      | [可选] 页面的描述                                            |
| `keywords`         | [可选] 页面的关键字                                          |
| `comments`         | [可选] 显示页面评论模块,默认值为`true`                       |
| `top_img`          | [可选] 控制顶部图片的显示，可选值`[空                        |
| `mathjax`          | [可选] 显示 `mathjax` 。当设置 `mathjax` 的 `per_page: false` 时，才需要配置，默认`false` |
| `katex`            | [可选] 显示`katex` 。当设置 `katex` 的 `per_page: false` 时，才需要配置，默认`false` |
| `aside`            | [可选] 控制侧边栏的显示，默认值为`true`                      |
| `aplayer`          | [可选] 在需要的页面加载`aplayer`的`js`和`css`                |
| `highlight_shrink` | [可选] 配置代码框是否展开，默认值为配置文件中的`highlight_shrink` |

**[注]:** 在填写参数时，冒号`:`后面得空一格，不然会报错

### Post front-matter

`Post front-matter`的参数如下：

| 参数                    | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `title`                 | [必填] 页面标题，归档时的名称默认也是使用`title`属性对应的值 |
| `date`                  | [必填] 页面创建日期                                          |
| `updated`               | [可选] 页面更新日期                                          |
| `tags`                  | [可选]文章标签                                               |
| `categories`            | [可选]文章分类                                               |
| `description`           | [可选] 页面的描述                                            |
| `keywords`              | [可选] 页面的关键字                                          |
| `top_img`               | [可选] 控制顶部图片的显示，可选值`[空                        |
| `cover`                 | [可选] 文章略缩图(如果没有设置`top_img`,文章页顶部将显示缩略图，可设为`false/图片地址/留空`) |
| `comments`              | [可选] 显示页面评论模块,默认值为`true`                       |
| `toc`                   | [可选] 显示文章`TOC`(目录树)，默认为设置中`toc`的`enable`配置) |
| `toc_number`            | [可选] 显示`toc_number`(默认为设置中`toc`的`number`配置)     |
| `copyright`             | [可选] 显示文章版权模块(默认为设置中`post_copyright`的`enable`配置) |
| `copyright_author`      | [可选] 文章版权模块的文章作者                                |
| `copyright_author_href` | [可选] 文章版权模块的文章作者链接                            |
| `copyright_url`         | [可选] 文章版权模块的文章链接                                |
| `copyright_info`        | [可选] 文章版权模块的版权申明文字                            |
| `mathjax`               | [可选] 显示 `mathjax` 。当设置 `mathjax` 的 `per_page: false` 时，才需要配置，默认`false` |
| `katex`                 | [可选] 显示`katex` 。当设置 `katex` 的 `per_page: false` 时，才需要配置，默认`false` |
| `aside`                 | [可选] 控制侧边栏的显示，默认值为`true`                      |
| `aplayer`               | [可选] 在需要的页面加载`aplayer`的`js`和`css`                |
| `highlight_shrink`      | [可选] 配置代码框是否展开，默认值为配置文件中的`highlight_shrink` |
| `sticky`                | [可选] 用于文章置顶，数值越大，被推荐程度越高                |

## link,tags,categories页面的创建和使用

三个页面的创建,在博客的根目录下执行如下三条命令

```SHELL
$ hexo new page link
$ hexo new page tags
$ hexo new page categories
```

执行完三条命令会在博客的根目录下的`source`文件夹中生成对应的`markdown`文件，文件的路径是：

```PLAINTEXT
source/link/index.md
source/tags/index.md
source/categories/index.md
```

执行完后，编辑各`index.md`文件，并在`front-matter`中添加`type`属性

- `link`对应的`index.md`添加`type: "link"`
- `tags`对应的`index.md`添加`type: "tags"`
- `categories`对应的`index.md`添加`type: "categories"`

### link链接的使用

在`Hexo`博客的根目录中的`source/_data`目录中创建一个`link.yml`文件。如果对应的目录和文件不存在就自行创建。创建完后，在`lingk.yml`文件中添加如下内容：

```YAML
- class_name: 友情链接
  class_desc: 那些人，那些事
  link_list:
    - name: Hexo
      link: https://hexo.io/zh-tw/
      avatar: https://d33wubrfki0l68.cloudfront.net/6657ba50e702d84afb32fe846bed54fba1a77add/827ae/logo.svg
      descr: 快速、简单且強大的博客框架

- class_name: 网站
  class_desc: 值得推荐的网站
  link_list:
    - name: Youtube
      link: https://www.youtube.com/
      avatar: https://i.loli.net/2020/05/14/9ZkGg8v3azHJfM1.png
      descr: 视频网站
    - name: Weibo
      link: https://www.weibo.com/
      avatar: https://i.loli.net/2020/05/14/TLJBum386vcnI1P.png
      descr: 中国最大社交分享平台
    - name: Twitter
      link: https://twitter.com/
      avatar: https://i.loli.net/2020/05/14/5VyHPQqR6LWF39a.png
      descr: 社交分享平台
```

其中`class_name`和`class_desc`支持`html`格式书写，如不需要，可以留空。

友情链接界面可以由用户自己自定义，只需要在友情链接的对应的`markdown`文档设置就行，以普通的 `markdown` 格式书写。

## 图集的显示

语法：

```MARKDOWN
<div class="gallery-group-main">
	{% galleryGroup <title> <description> <link> <img_url> %}
	{% galleryGroup <title> <description> <link> <img_url> %}
	{% galleryGroup <title> <description> <link> <img_url> %}
	......
</div>
```

- `title`: 图库标题
- `description`: 图库的描述
- `link`: 链接到对应图库的地址
- `img_url`: 图库封面的地址

使用实例：

```MARKDOWN
<div class="gallery-group-main">
    {% galleryGroup '壁紙' '收藏的一些壁紙' '/galleries/post' https://i.loli.net/2019/11/10/T7Mu8Aod3egmC4Q.png %}
    {% galleryGroup '漫威' '關於漫威的圖片' '/Gallery/marvel' https://i.loli.net/2019/12/25/8t97aVlp4hgyBGu.jpg %}
    {% galleryGroup 'OH MY GIRL' '關於OH MY GIRL的圖片' '/Gallery/ohmygirl' https://i.loli.net/2019/12/25/hOqbQ3BIwa6KWpo.jpg %}
</div>
```

### 图片展示页面

图片展示页面也是普通的页面，直接使用`hexo new page <pageName>`就行

语法:

```MARKDOWN
{% gallery %} 
    ![](https://i.loli.net/2019/12/25/Fze9jchtnyJXMHN.jpg) 
    ![](https://i.loli.net/2019/12/25/ryLVePaqkYm4TEK.jpg) 
    ![](https://i.loli.net/2019/12/25/gEy5Zc1Ai6VuO4N.jpg) 
    ![](https://i.loli.net/2019/12/25/d6QHbytlSYO4FBG.jpg) 
    ![](https://i.loli.net/2019/12/25/6nepIJ1xTgufatZ.jpg) 
    ![](https://i.loli.net/2019/12/25/E7Jvr4eIPwUNmzq.jpg) 
    ![](https://i.loli.net/2019/12/25/mh19anwBSWIkGlH.jpg) 
    ![](https://i.loli.net/2019/12/25/2tu9JC8ewpBFagv.jpg) 
    .....
{% endgallery %}
```

通过`{% gallery %}`和`{% endgallery %}` 包围的图片，`hexo`会自动根据图片的大小对图片进行排版