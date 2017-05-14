---
title: Hexo介绍及常用安装使用
date: 2016-11-27 21:59:22
tags: hexo,github
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

这条命令用来创建一篇新文章，用hexo new创建的文章格式是md，和普通markdown文档不一样，它包含了当前layout提供的头部模板，如post layout布局的文章，顶部会出现：

	---
	title: Hexo介绍及常用安装使用
	date: 2016-11-27 21:59:22
	tags: hexo
	---

在_config.yml中会有default_layout这个参数，默认是post，也就是说不加`layout`参数时，`new`出来的文章使用post布局。当然，default_layout可以根据自己的需要进行更改。

#### layout

Hexo 有三种默认布局：post、page 和 draft，它们分别对应不同的路径，而你自定义的其他布局和 post 相同，都将储存到 source/_posts 文件夹。

|布局	|路径  |
|:------|---- |
|post	|source/_posts |
|page	|source |
|draft	|source/_drafts |

上面说过post布局的样子，对于page和draft，你可以尝试用它们 new 一篇新文章来看看效果。


#### 文件名称

Hexo 默认以标题做为文件名称，但你可以编辑_config.yml中的 new_post_name 参数来改变默认的文件名称，举例来说，设为 :year-:month-:day-:title.md 可让您更方便的通过日期来管理文章。

| 变量 | 描述 |
|:----|------|
| :title | 标题（小写，空格将会被替换为短杠）|
| :year  | 建立的年份，比如， 2016 |
| :month | 建立的月份（有前导零），比如， 04 |
| :i_month| 建立的月份（无前导零），比如， 4 |
| :day | 建立的日期（有前导零），比如， 07 |
| :i_day | 建立的日期（无前导零），比如， 7 |

文件创建成功，剩下的就是用markdown语法进行愉快的创作了！


### 生成及部署

#### 生成

文档编写完成后，想要对外发布就得先进行文章的解析和资源的生成：

`$ hexo generate`

该命令生成静态网页文件。

#### 本地启动

生成完可以在本地进行启动测试：

`$ hexo server`

启动后，可以通过`http://localhost:4000/`访问本地blog。

| 选项 | 描述 |
|:----|------|
| -p,--port | 重设端口 |
| -s,--static | 只使用静态文件 |
| -l,--log | 启动日记记录，使用覆盖记录格式 |

#### 部署

`$ hexo deploy`

该命令用来对generate后的资源进行部署，但是注意，使用该命令之前，需要安装hexo-deployer-git插件：

`$ npm install hexo-deployer-git`

安装完成，我们可以进行部署，这里我们还得明确一点，部署到哪？

在_config.yml中可以指定部署目的地：


	deploy:
	- type: git
	  repo:
	- type: heroku
	  repo:

对于git方式来说，repo建议使用ssh协议而非https协议，还有，你需要提前在github上配置好当前机器的公钥。

这个指定的git repo是一个[**github pages**](https://pages.github.com/)，他是github为方便程序员们发布项目的说明文档而开放的服务，而 gh Pgaes 的美好之处在于，它有足以胜任成为一个独立博客的能耐。这里不详细介绍，你可以去官网学习。


在deploy之后，访问github pages地址你会发现文档已经全部发布。

![](http://i.imgur.com/v1K2xKw.jpg)

如果嫌麻烦，可以执行:

`$ hexo generate -d`

or

`$ hexo g -d`

将编译和部署合并成一步操作

#### 清理

`$ hexo clean`

清理掉以生成的静态网页相关文件


### 针对github上已有的blog仓库

实际工作中，往往是已经有一个blog仓库，我们需要更新它到本地并用hexo去管理。

由于hexo管理下的node_modules文件夹过大，一般我们不会将其放在github中，所以clone下来的文件夹中没有node_modules，需要执行如下命令进行node_modules的安装：

	$ npm install

这种情况下不需要执行初始化`hexo init`了，之后的其他操作都还是一样的。


**注：以上对于hexo的常用知识进行了介绍，如果你还需要了解更详细的内容，从[这里](https://hexo.io/docs/)到官网进行学习。**