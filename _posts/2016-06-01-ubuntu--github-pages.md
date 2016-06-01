---
layout:     post
title:      "ubuntu16.04使用Github Pages + Jekyll搭建博客"
subtitle:   " \"使用markdown编写\""
date:       2016-06-01 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - jekyll
    - github pages
    - ubuntu
    - linux
---

##  ubuntu16.04使用Github Pages + Jekyll搭建博客

### 第一步 需要有github账号

[注册地址](https://github.com/)

 注：**连不上就有可能被墙了**
 

### 第二步 安装git

```bash
#安装git
sudo apt-get install git 
#安装smartgit
sudo add-apt-repository ppa:eugenesan/ppa
sudo apt-get update
sudo apt-get install smartgit

```

注：初始化时选择非商业

### 第三步 创建github pages

- 进入个人页面
- 选择`new repository`
- `repository name`处填写你github pages名字。（例如:xxxxx.github.com）
- 进入你刚创建的`repository`的`Settings`
- 找到`GitHub Pages`部分
- 点击`Launch automatic page generator`进入到另一个页面
- 点击`Continue to Layouts`
- 选择主题，点击`Publish`按钮

把刚创建的`repository`，`clone`到本地

```bash
git clone xxx.git
```

[官方文档](https://pages.github.com/)


### 第四步 安装jekyll

```bash
#安装ruby
sudo apt-get install ruby ruby-dev
#更换Gem sources，国外sources下载有问题。
gem sources #显示sources
sudo gem sources –r https://rubygems.org/
sudo gem sources –r http://rubygems.org/
sudo gem sources -a http://ruby.taobao.org/
gem sources -u #更新source cache
sudo gem install jekyll
sudo gem install rdiscount
sudo gem install bundler
```


在刚才`clone`到本地的`repository`的目录下，运行`jekyll server`，然后浏览器打开http://localhost:4000 , 即可检查你的github pages的效果了


### 第五步 使用模板

你可以在网上找一些好看模板，来替换的你`github pages`

- [jekyllthemes.org](http://jekyllthemes.org/)

- [mademistakes ](https://mademistakes.com/work/jekyll-themes/)

- [Hux Blog](https://github.com/Huxpro/huxpro.github.io)(我使用的是这个)

下载完成后替换到`repository`目录下


#### jekyll3.0以前的版需更新

```bash
sudo gem update jekyll # 更新jekyll
sudo gem update github-pages #更新依赖的包
```
#### 修改_config.yml来搭建自己的博客

```
# Site settings
title: Hux Blog             # 你的博客网站标题
SEOTitle: Hux Blog          # 在后面会详细谈到
description: "Cool Blog"    # 随便说点，描述一下

# SNS settings      
github_username: huxpro     # 你的github账号
weibo_username: huxpro      # 你的微博账号，底部链接会自动更新的。

# Build settings
# paginate: 10              # 一页你准备放几篇文章
```

#### 写文章

要发表的文章一般以`markdown`的格式放在这里`_posts/`，模板头部信息如下：

```
---
layout:     post
title:      "Hello 2015"
subtitle:   "Hello World, Hello Blog"
date:       2015-01-29 12:00:00
author:     "Hux"
header-img: "img/post-bg-2015.jpg"
tags:
    - Life
---
```

#### 右边栏

设置是在`_config.yml`文件里面的`Sidebar settings`那块。

```
# Sidebar settings
sidebar: true  #添加侧边栏
sidebar-about-description: "简单的描述一下你自己"
sidebar-avatar: /img/avatar-hux.jpg     #你的大头贴，请使用绝对地址
```
#### 评论

`多说`
优点是：支持国内各主流社交软件(微博，微信，豆瓣，QQ空间 ...)一键分享按钮功能，另外登陆比较方便，管理界面也是纯中文的，相对于disqus全英文的要容易操作一些；
缺点是：就是界面丑了一点。 当然你是可以自定义界面的css的，详情请看多说开发者文档 http://dev.duoshuo.com/docs/5003ecd94cab3e7250000008 。

##### 注册多说

- [注册地址](http://duoshuo.com/)
- http://duoshuo.com,点击我要安装，注册你的多说二级域名。去掉 .duoshuo.com,就是你的short_name

`_config.yml`文件

```
duoshuo_username: _你的用户名_
```

#### 网站分析

网站分析，现在支持百度统计和Google Analytics。需要去官方网站注册一下，然后将返回的code贴在下面：

```
# Baidu Analytics
ba_track_id: 

# Google Analytics
ga_track_id: 'UA-xxxxxx-xx'        # 你用Google账号去注册一个就会给你一个这样的id
ga_domain:         # 默认的是 auto, 这里我是自定义了的域名，你如果没有自己的域名，需要改成auto。
```

[使用文档](https://github.com/Huxpro/huxpro.github.io/blob/master/README.zh.md)

