title: 使用Hexo建站的注意事项

date: 2015-12-20 19:54:54

tags: iOS开发
---

### 为什么使用Hexo建站

首先要感谢公司同事[70kg](https://github.com/70kg)，我之前的博客也只是托管在gitpage上，而且没有博文的，使用的是jakyll。无意中看了70kg的博客，感觉很棒，就决定和他一样改用hexo建站。

### 建站前戏

因为我使用的是Mac，所以环境是OS X 10.11.2 EI Capitan

首先要安装:

* [`Node.js`](https://nodejs.org/en/)
* [`Git`](http://git-scm.com/)

### 安装Hexo

如果已经安装Node.js和git的话，可以在终端打命令安装hexo了:

​	

``` 
$ npm install -g hexo-cli
```

> #### 注意：此处如果是OS X环境下报了error的话，需要注意两点:
> 
> 1. ##### Command Line Tools
>    
>    你要先去App Store安装Xcode，然后在** Preferences -> Download -> Command Line Tools -> Install**这里去安装Command Line Tools
>    
> 2. ##### sudo模式下安装
>    
>    可能会出现npm安装之后提示让你用管理员模式安装的error，所以这个时候就要在sudo模式下安装，终端下:

``` 
sudo npm install --unsafe-perm --verbose -g hexo
```

### 初始化Hexo

在终端下进入你喜爱的文件夹下，用命令对hexo初始化:

``` 
hexo init
```

​	

初始化完成之后，我们要安装依赖包

``` 
npm install
```

​	

> 注意：此处如果有问题，可能是安装依赖包出现问题，尝试在sudo模式下安装来解决问题:

``` 
sudo npm install
```

​	

### 本地查看

现在基本上建站完成了，可以在本地服务器跑一下看看效果了。终端在hexo文件目录下输入:

​	

``` 
hexo generate
hexo server
```

​	

然后进入浏览器打开`localhost:4000`查看效果吧

### 绑定域名

因为我之前就已经在gitPage上建好博客了，所以只需要终端:

``` 
hexo deploy
```

​	

就可以把hexo提交上去，在gitPage上建站这方面的教程可以看[Zippera's blog](http://zipperary.com/categories/hexo/)，使用Hexo建站说明很详细

> 绑定域名需要在hexo本地source目录中添加CNAME文件，内容就是购买好的域名。CNAME内容: 



``` 
zcill.com
```

这个时候使用命令:

``` 
hexo g
hexo d
```

​	

就可以完成hexo的基本安装了

### 常用命令

``` 
hexo g == hexo generate
hexo d == hexo deploy
hexo s == hexo server
hexo n "我的第一篇博客" == hexo new "我的第一篇博客"
```

这些命令可以使用简写，更加方便

### 感谢

* [hero 配置、部署 错误 - SegmentFault](http://segmentfault.com/a/1190000002632530)
* [hero 配置教程 -Zipperary's Blog](http://zipperary.com/2013/05/28/hexo-guide-2/)
* [70kg.info](http://70kg.info)