title: 在Objective-C项目中使用swift写的第三方库
date: 2016-01-23 01:09:32
tags: iOS
---
无意中发现一个高质量的轻量的第三方库，使用swift写的，并没有Objective-C写的版本，[TextFieldEffects](https://github.com/raulriera/TextFieldEffects)。无奈自己没学过swift，想使用这个库就只能去实现一下OC和swift混编了。

<div class="github-widget" data-repo="raulriera/TextFieldEffects"></div>

#### 更改Defines Module
在Build Settings中改Packing的Defines Module为Yes，这个时候Xcode会默认生成一个(PRODUCT_NAME)-Swift.h，不过这个文件在项目目录是看不见的。(PRODUCT_NAME)是工程名，我的项目名是叫Meizi，那么生成的文件就是Meizi-Swift.h。

![改Module](http://7xq5gj.com1.z0.glb.clouddn.com/在OC项目中使用swift写的第三方库/改Module.png)

#### 导入(PRODUCT_NAME)-Swift.h
进入需要使用swift第三方库的类，导入-Swift.h文件

```
#import "Meizi-Swift.h"
```
![报错](http://7xq5gj.com1.z0.glb.clouddn.com/在OC项目中使用swift写的第三方库/报错.png)

#### 创建桥接文件
这个时候可能会报错，因为工程中没有bridge桥接文件。这个时候要去创建一个swift文件了，可以创建一个test.swift文件

![创建swift文件](http://7xq5gj.com1.z0.glb.clouddn.com/在OC项目中使用swift写的第三方库/创建swift文件.png)

![创建桥接文件](http://7xq5gj.com1.z0.glb.clouddn.com/在OC项目中使用swift写的第三方库/创建桥接文件.png)

创建完了记得点击Create Bridging Header，Xcode会默认帮你创建好桥接文件。我们需要的就是这个桥接文件，这个时候可以把test.swift文件删除了。

#### 导入swift写的第三方库
![拖入swift库](http://7xq5gj.com1.z0.glb.clouddn.com/在OC项目中使用swift写的第三方库/拖入swift库.png)

这个时候把需要使用的库文件拖入工程就可以直接调用。

#### 大功告成
![正常使用](http://7xq5gj.com1.z0.glb.clouddn.com/在OC项目中使用swift写的第三方库/正常使用.png)

如果这个swift库不是`only for swift`的话，也可以使用Objective-C的方法去调用的。
