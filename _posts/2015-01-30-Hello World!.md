---
layout: post
title: Hello World!
description: "第一篇博客"
modified: 2015-01-30
tags: [随笔, Jekyll]
---

### 在熊大伯的怂恿下，折腾了个自己的静态博客。原先对博客的看法就是要么找个门户网站，开个马甲号，写写每天吃了啥做了啥，分享记录些编程相关的知识。要么自己从后台搭到前台，每篇文章的界面自己慢慢写，然而我对HTML的布局又非常不感冒，所以一直没想过开个博客。

###直到上周五，熊大伯说了Github静态博客的东西，略微一听，哎~感觉有点意思，这么搞个博客挺符合我小清新的口味，又不折腾又有一点点Geek的感觉。

###作为第一篇博客，就在这里把搭建的大概流程说下吧~也作为是分享。其实这是一个很简单的事情，不论你是不是程序猿，都能拥有这么个炫酷狂拽的静态博客。所以有兴趣的话不要担心自己弄不了~

##Step 1
首先，你要注册一个[GitHub](https://github.com/)账号，因为[GitHub](https://github.com/)提供了每个账号300MB的控件来搭建静态博客，十分厚道。我也选择在这里创建我的这个博客。
然后创建一个新的[Repository](https://github.com/new)名字为*username*.github.io

> username为你的GitHub用户名，比如我是KarelGT，那么就应该起名为KarelGT.github.io

创建完之后，把这个Repository Clone到本地，在工程的根目录下创建一个*index.html*的文件。里边可以写上下面的代码：
{% highlight html %}
<!DOCTYPE html>
<html>
    <body>
        <h1>Hello World</h1>
        <p>I'm hosted with GitHub Pages.</p>
    </body>
</html>
{% endhighlight %}
再将其Push到GitHub。

> index.html是你的博客首页，先写这写，来验证目前是否成功
到这一步为止，在浏览器里输入http://*username*.github.io，会发现已经能在互联网上显示你刚才写的东西了，而且已经能被所有人看到啦！

> 以上步骤可以参考更详细的说明[GitHub Pages](https://pages.github.com/)

##Step 2
这里要用到一个工具Jekyll，用来帮助我们更方便的搭建博客。使用之前，先要确保[Ruby](https://www.ruby-lang.org/en/)和[RubyGems](https://rubygems.org/gems)。MacOS下应该是默认装好了的。
有了以上环境，安装Jekyll只需要一个命令：
{% raw %}
    ~$ gem install jekyll
{% endraw %}
就可以完成安装啦。


>1. gem install jekyll命令可能需要加上sudo提升权限
2. 如果因为某种神秘的力量导致Jekyll在朝内无法下载，那么这时候你可以使用Taobao提供的[镜像地址](http://ruby.taobao.org/)来下载，速度非常给力！（Thx熊大伯提供的解决方案）

接下来就可以用
{% raw %}
    ~$ jekyll new *myblog*
    ~$ cd *myblog*
    ~/*myblog* $ jekyll serve
{% endraw %}
来创建一个新的博客工程，并且在本地启动，可以在http://127.0.0.1:4000/进行浏览。

> myblog为你想起的工程名字

> 以上步骤可以参考更详细的说明[Jekyll官网](http://jekyllrb.com/)

##Step 3
以上两步做完，最后就是看例子明白博文放哪里，改些自己的配置信息。再将你用Jekyll创建的工程Push到github.io，就已经完成所有的工作了。你已经能够开始写博客了。
但是如果需要更好的使用自己的博客，你可能还需要了解Markdown语法。还可以自己替换一些主题。

###目前我也在继续摸索，不过这的确是一个很有意思的东西，以后会在这里写一些有的没的~
写完收工，不知道这篇Hello World!能顺利部署上去不~

2015-02-13 补充：有些主题需要用bundle exec jekyll build来build。bundle exec jekyll serve来run。