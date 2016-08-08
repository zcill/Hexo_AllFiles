title: 抓取已经在AppStore上架的App素材

date: 2016-1-16 23:55:11

tags: iOS开发
---

### zcill带你完整地抓取一个App的素材

首先，你要到iTunes Store中去下载需要App的ipa安装包![到iTunes Store去下载App](http://7xq5gj.com1.z0.glb.clouddn.com/20160116_iTunesStore.png)

然后在这个目录下把ipa解压出来

![文件路径1](http://7xq5gj.com1.z0.glb.clouddn.com/20160116_File1.png)

![文件路径2](http://7xq5gj.com1.z0.glb.clouddn.com/20160116_File2.png)

然后进入解压出来的文件目录下右击显示包内容

![显示包内容](http://7xq5gj.com1.z0.glb.clouddn.com/20160116_showFile.png)

到这里为止，是不是感觉包里面的图片好少啊，怎么全是xib编译后生成的nib文件，怎么没有图了？

> 你们注意到那个Assets.car文件了么？是不是感觉很熟悉?

![Xcode目录](http://7xq5gj.com1.z0.glb.clouddn.com/Snip20160116_7.png)

这货怎么和Xcode中的Asset图片集一个名字？

请使用这个软件来打开car文件[ThemeEngine](https://github.com/alexzielenski/ThemeEngine)，在release目录下，下载最新版即可。

![ThemeEngine](http://7xq5gj.com1.z0.glb.clouddn.com/20160116_ThemeEngineDetail.png)

这个时候就可以打开并显示，可以像Adobe PhotoShop那样修改素材啦！