# hexo butterfly环境配置



## 官方文档

https://butterfly.js.org/posts/dc584b87/

## nodejs

https://nodejs.org/en

## hexo

```bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

> Mac 需要sudo install

## 安装butterfly

```
git clone -b master https://gitee.com/immyw/hexo-theme-butterfly.git themes/butterfly
```

```
sudo npm install hexo-renderer-pug hexo-renderer-stylus --save
```

## 配置butterfly

### 页面配置

#### Front-matter
`Front-matter` 是 `markdown` 文件最上方以 `---` 分隔的区域，用于指定个别档案的变数。

`Page Front-matter` 用于页面配置
`Post Front-matter` 用于文章页配置



**Page Front-matter**

```markdown
---
title:
date:
updated:
type:
comments:
description:
keywords:
top_img:
mathjax:
katex:
aside:
aplayer:
highlight_shrink:
random:
---
```



| 写法             | 解释                                                         |
| ---------------- | ------------------------------------------------------------ |
| title            | 【必需】页面标题                                             |
| date             | 【必需】页面创建日期                                         |
| type             | 【必需】标签、分类和友情链接三个页面需要配置                 |
| updated          | 【可选】页面更新日期                                         |
| description      | 【可选】页面描述                                             |
| keywords         | 【可选】页面关键字                                           |
| comments         | 【可选】显示页面评论模块 (默认 true)                         |
| top_img          | 【可选】页面顶部图片                                         |
| mathjax          | 【可选】显示mathjax (当设置mathjax的per_page: false时，才需要配置，默认 false) |
| katex            | 【可选】显示katex (当设置katex的per_page: false时，才需要配置，默认 false) |
| aside            | 【可选】显示侧边栏 (默认 true)                               |
| aplayer          | 【可选】在需要的页面加载aplayer的js和css,请参考文章下面的音乐 配置 |
| highlight_shrink | 【可选】配置代码框是否展开 (true/false) (默认为设置中highlight_shrink的配置) |
| random           | 【可选】配置友情链接是否随机排序（默认为 false)              |

**Post Front-matter**

```markd
---
title:
date:
updated:
tags:
categories:
keywords:
description:
top_img:
comments:
cover:
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
mathjax:
katex:
aplayer:
highlight_shrink:
aside:
abcjs:
---
```

| 写法                  | 解释                                                         |
| --------------------- | ------------------------------------------------------------ |
| title                 | 【必需】文章标题                                             |
| date                  | 【必需】文章创建日期                                         |
| updated               | 【可选】文章更新日期                                         |
| tags                  | 【可选】文章标签                                             |
| categories            | 【可选】文章分类                                             |
| keywords              | 【可选】文章关键字                                           |
| description           | 【可选】文章描述                                             |
| top_img               | 【可选】文章顶部图片                                         |
| cover                 | 【可选】文章缩略图(如果没有设置top_img,文章页顶部将显示缩略图，可设为false/图片地址/留空) |
| comments              | 【可选】显示文章评论模块(默认 true)                          |
| toc                   | 【可选】显示文章TOC(默认为设置中toc的enable配置)             |
| toc_number            | 【可选】显示toc_number(默认为设置中toc的number配置)          |
| toc_style_simple      | 【可选】显示 toc 简洁模式                                    |
| copyright             | 【可选】显示文章版权模块(默认为设置中post_copyright的enable配置) |
| copyright_author      | 【可选】文章版权模块的文章作者                               |
| copyright_author_href | 【可选】文章版权模块的文章作者链接                           |
| copyright_url         | 【可选】文章版权模块的文章连结链接                           |
| copyright_info        | 【可选】文章版权模块的版权声明文字                           |
| mathjax               | 【可选】显示mathjax(当设置 mathjax 的 per_page: false 时，才需要配置，默认 false ) |
| katex                 | 【可选】显示 katex (当设置 katex 的 per_page: false 时，才需要配置，默认 false ) |
| aplayer               | 【可选】在需要的页面加载 aplayer 的 js 和 css,请参考文章下面的音乐 配置 |
| highlight_shrink      | 【可选】配置代码框是否展开(true/false)(默认为设置中 highlight_shrink 的配置) |
| aside                 | 【可选】显示侧边栏 (默认 true)                               |
| abcjs                 | 【可选】加载 abcjs (当设置 abcjs 的 per_page: false 时，才需要配置，默认 false ) |

#### 标签页
1. 前往你的 `Hexo` 博客的根目录

2. 输入 `hexo new page tags`

3. 你会找到 `source/tags/index.md` 这个文件

4. 修改这个文件：

   记得添加 `type: "tags"`

```markdown
---
title: 标签
date: 2018-01-05 00:00:00
type: "tags"
orderby: random
order: 1
---
```

| 参数    | 解释                                                         |
| ------- | ------------------------------------------------------------ |
| type    | 【必须】页面类型，必须为 tags                                |
| orderby | 【可选】排序方式 ：random/name/length                        |
| order   | 【可选】排序次序： 1, asc for ascending; -1, desc for descending |


#### 分类页
1. 前往你的 Hexo 博客的根目录

2. 输入 hexo new page categories

3. 你会找到 source/categories/index.md 这个文件

4. 修改这个文件：

   记得添加 `type: "categories"`

```markdown
---
title: 分类
date: 2018-01-05 00:00:00
type: "categories"
---
```



#### 404页面

主题内置了一个简单的 404 页面，可在设置中开启

本地预览时，访问出错的网站是不会跳到 404 页面的。

如需本地预览，请访问 http://localhost:4000/404.html

```yaml
# A simple 404 page
error_404:
  enable: true
  subtitle: "页面没有找到"
  background: 
```

