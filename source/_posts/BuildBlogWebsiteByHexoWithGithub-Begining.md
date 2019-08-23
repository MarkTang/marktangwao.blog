---
title: Hexo+GitHub搭建免费个人博客教程 - 无坑版
date: 2019-08-10 20:37:43
description: 这不是一篇非常基础的搭建教程；
category: [随笔]
tags: [Blog]
---



#### 0、准备

这个不是一篇基础的搭建教程。

你需要了解并会简单使用如下几个知识：*(如果不清楚，请自行网上搜索，资料非常多)*

>1. Git和GitHub的使用；
>2. Node和NPM的使用；

**继续往下读文章，就当你会使用如上两个知识点**



#### 1、在GitHub上面创建Repository

新建一个名为`<username>.github.io`的仓库，如果你的github用户名是lykantang，那么你就新建`lykantang.github.io`的仓库（必须是你的用户名，其它名称无效），将来你的网站访问地址就是http://lykantang.github.io 了，是不是很方便？

由此可见，每一个github账户最多只能创建一个这样可以直接使用域名访问的仓库。

几个注意的地方：

>1. 注册的邮箱一定要验证，否则不会成功；
>2. 仓库名字必须是：`<username>.github.io`，其中`<username>`是你的用户名；
>3. 仓库创建成功不会立即生效，需要过一段时间，大概10-30分钟，或者更久，我的等了大概十五分钟左右才生效；

创建成功后，默认会在你这个仓库里生成一些示例页面，以后你的网站所有代码都是放在这个仓库里了。



#### 2、Hexo安装及简单使用

##### 2.1、Hexo简介

Hexo是一个简单、快速、强大的基于 Github Pages 的博客发布工具，支持Markdown格式，有众多优秀插件和主题。

官网： http://hexo.io
github: https://github.com/hexojs/hexo



##### 2.2、原理

由于github pages存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动态内容，假如每次写完一篇文章都要手动更新博文目录和相关链接信息，相信谁都会疯掉，所以Hexo所做的就是将这些md文件都放在本地，每次写完文章后调用写好的命令来批量完成相关页面的生成，然后再将有改动的页面提交到github。



##### 2.3、注意事项

安装之前先来说几个注意事项：

>	1. 很多命令既可以用Windows的cmd来完成，也可以使用git bash来完成，但是部分命令会有一些问题，为避免不必要的问题，建议全部使用git bash来执行；
>	2. Hexo不同版本差别比较大，网上很多文章的配置信息都是基于2.x的，所以注意不要被误导；
>	3. Hexo有2种`_config.yml`文件，一个是根目录下的全局的`_config.yml`，一个是各个`theme`下的；



##### 2.4、安装

~~~bash
npm install -g hexo-cli
~~~



##### 2.5、初始化

```bash
cd /f/blog/
hexo init
```

Hexo会自动下载一些文件到这个目录，包括node_modules，目录结构如下图：

![](/resource/image/BuildBlogWebsiteByHexoWithGithub-Begining/hexoinit.png)



~~~bash
hexo g # 生成public静态文件夹
hexo s # 启动服务
~~~

执行以上命令之后，Hexo就会在public文件夹生成相关html文件，这些文件将来都是要提交到github去的：

![](/resource/image/BuildBlogWebsiteByHexoWithGithub-Begining/hexopublic.png)

`hexo s`是开启本地预览服务，打开浏览器访问 http://localhost:4000 即可看到内容。

第一次初始化的时候hexo已经帮我们写了一篇名为 Hello World 的文章，默认的主题比较丑，打开时就是这个样子：

![](/resource/image/BuildBlogWebsiteByHexoWithGithub-Begining/hexodefault.png)



##### 2.6、Hexo换皮肤：由默认皮肤换成yilia

既然默认主题很丑，那我们别的不做，首先来替换一个好看点的主题。这是[官方主题](https://hexo.io/themes/)。

个人比较喜欢的2个主题：[hexo-theme-yilia](https://github.com/litten/hexo-theme-yilia)和[hexo-theme-jekyll](https://github.com/pinggod/hexo-theme-jekyll)。

来下载yilia这个主题：

~~~bash
cd /f/blog/
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
~~~

下载后的主题都在这里：

![](/resource/image/BuildBlogWebsiteByHexoWithGithub-Begining/hexotheme.png)

修改跟目录下`_config.yml`中的`theme: landscape`改为`theme: yilia`，然后重新执行`hexo g`来重新生成。

`hexo s`查看效果，如图：

![](/resource/image/BuildBlogWebsiteByHexoWithGithub-Begining/hexothemeyilia.png)

如果出现一些莫名其妙的问题，可以先执行`hexo clean`来清理一下public的内容，然后再来重新生成和发布。



##### 2.7、上传到github

如果你一切都配置好了，发布上传很容易，一句`hexo d`就搞定，当然关键还是你要把所有东西配置好。

首先，`ssh key`肯定要配置好。

其次，配置根目录下的`_config.yml`中有关deploy的部分：

正确写法：

~~~bash
deploy:
  type: git
  repository: git@github.com:<username>/<username>.github.io.git
  branch: master
~~~

最后，安装`hexo-deployer-git`插件：

~~~bash
npm install --save hexo-deployer-git
~~~



##### 2.8、常用Hexo命令

常见命令

~~~bash
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
~~~

缩写：

~~~bash
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
~~~

组合命令：

~~~bash
hexo s -g #生成并本地预览
hexo d -g #生成并上传
~~~



##### 2.9、根目录下的_config.yml

这里面都是一些全局配置，每个参数的意思都比较简单明了，所以就不作详细介绍了。

需要特别注意的地方是，冒号后面必须有一个空格，否则可能会出问题。



##### 2.10、写博客

定位到我们的hexo根目录并执行命令：

~~~bash
cd /f/blog/
hexo new 'first-blog'
~~~

Hexo会帮我们在`_posts`下生成相关md文件：

![](/resource/image/BuildBlogWebsiteByHexoWithGithub-Begining/hexofirstblog.png)

我们只需要打开这个文件就可以开始写博客了，默认生成如下内容：

![](/resource/image/BuildBlogWebsiteByHexoWithGithub-Begining/hexofirstblogwrite.png)

最终部署时生成：`hexo\public\first-blog\index.html`，但是它不会作为文章出现在博文目录。



#### 3、最终效果

可以访问我的git博客来查看效果：[marktangwao.com](https://marktangwao.com)

不过呢，其实这个博客我只是拿来玩一玩的，没打算真的把它当博客，因为我已经有一个自己的博客了，哈哈！正因如此，本文仅限入门学习，关于hexo搭建个人博客的更高级玩法大家可以另找教程。



#### 4、参考

[如何搭建一个独立博客——简明Github Pages与Hexo教程](https://www.jianshu.com/p/05289a4bc8b2)

[Hexo搭建Github静态博客](https://www.cnblogs.com/zhcncn/p/4097881.html)





























