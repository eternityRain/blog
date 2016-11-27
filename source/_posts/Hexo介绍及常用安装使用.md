---
title: Hexo介绍及常用安装使用
date: 2016-11-27 21:59:22
tags: hexo
---

## hexo简介

hexo是使用nodejs开发的一个快速、简洁、高效、开源的静态博客生成器。

hexo使用Markdown解析文章，因此，文章需采用Markdown语法编写，在几秒内，即可利用靓丽的主题生成静态网页。类似于jekyll、Octopress、Wordpress，我们可以用hexo创建自己的博客，托管到github，绑定自己的域名。

## 选择hexo的理由

市场上也有不少其他类似hexo的博客生成器，为什么选择hexo呢？

- 非常小巧，使用nodejs开发，下载安装时只需要几个简答的npm命令即可

- 纯静态博客，不需要额外的数据库、php环境…仅仅需要一个nodejs环境即可

- 由于是静态博客，所以，可以非常方便部署到github

- 支持markdown语法、兼容windows linux mac，高可扩展性和自定义，而且有非常多的hexo主题

## 安装hexo

这里我们针对windows环境进行演示说明，其他环境大体上与此一致，详细请参照[官方文档](https://hexo.io/zh-cn/docs/index.html)。

在安装hexo之前，你需要保证环境中有Node.js和Git环境，这里我们就不赘述他们俩的安装。

在Node.js和Git安装无误情况下，执行：

`$ npm install -g hexo-cli`

通过npm进行hexo的全局安装，这么简单，一条命令hexo即安装完成。

## hexo使用

### 建站

安装hexo完成后，就可以进行使用了。

首先，对于不是从github更新的hexo库，我们需要自己初始化本地hexo库：

	$ hexo init <folder>
	$ cd <folder>
	$ npm install


初始化完成后目录下有如下文件：

![](http://i.imgur.com/BOb0Rtx.png)

#### 结构说明

##### _config.yml

网站的[配置](https://hexo.io/docs/configuration.html)信息，可以在此配置大部分的参数。

##### package.json

应用程序的信息。EJS, Stylus 和 Markdown renderer 已默认安装，你可以自由移除。

	package.json
	{
	  "name": "hexo-site",
	  "version": "0.0.0",
	  "private": true,
	  "hexo": {
	    "version": ""
	  },
	  "dependencies": {
	    "hexo": "^3.0.0",
	    "hexo-generator-archive": "^0.1.0",
	    "hexo-generator-category": "^0.1.0",
	    "hexo-generator-index": "^0.1.0",
	    "hexo-generator-tag": "^0.1.0",
	    "hexo-renderer-ejs": "^0.1.0",
	    "hexo-renderer-stylus": "^0.2.0",
	    "hexo-renderer-marked": "^0.2.4",
	    "hexo-server": "^0.1.2"
	  }
	}

##### scaffolds

模版 文件夹。当你新建文章时，Hexo 会根据 scaffold 来建立文件。

##### source

资源文件夹是存放用户资源(blog)的地方。

##### public

Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。

##### themes

主题文件夹。Hexo会根据主题来生成静态页面。

这样即建站成功。

### 写作

完成建站，接下来当然是开始写作了。

`$ hexo new [layout] <title>`

这条命令用来创建一篇新文章