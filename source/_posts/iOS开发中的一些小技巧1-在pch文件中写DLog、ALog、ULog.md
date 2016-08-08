title: iOS开发中的一些小技巧1-在pch文件中写DLog、ALog、ULog
date: 2016-01-21 11:58:02
tags: tips
---
在实际开发中，经常使用NSLog去打印一些我们想看到的东西，不过这些东西在打包成为Release版后，都不应该再出现。为了方便我们在Debug模式下打印，同时Release版本不再Log，可以在pch中写几个打印方法，全局都可以调用，方便开发。

DLog - Debug模式下打印输出在控制台，Release模式不打印

```
// DLog will output like NSLog only when the DEBUG variable is set

#ifdef DEBUG

#define DLog(fmt, ...) NSLog((@"%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);

#else

#define DLog(...)

#endif
```
ALog - 无论Debug模式还是Release模式都会打印，和NSLog一样

```
// ALog will always output like NSLog

#define ALog(fmt, ...) NSLog((@"%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
```
ULog - 使用UIAlertView去弹出需要打印出的内容

```
// ULog will show the UIAlertView only when the DEBUG variable is set

#ifdef DEBUG

#define ULog(fmt, ...)  { UIAlertView *alert = [[UIAlertView alloc] initWithTitle:[NSString stringWithFormat:@"%s\n [Line %d] ", __PRETTY_FUNCTION__, __LINE__] message:[NSString stringWithFormat:fmt, ##__VA_ARGS__]  delegate:nil cancelButtonTitle:@"Ok" otherButtonTitles:nil]; [alert show]; }

#else

#define ULog(...)

#endif

```
> 详见[Stack Overflow](http://stackoverflow.com/questions/9659763/difference-between-nslog-and-dlog)


Xcode6之后，pch文件需要自己去创建并设置编译器去编译，所以要设置pch的路径，注意合理搭配`$(SRCROOT)` `$(PRODUCT_NAME)` `$(PRODUCT_DIR)`去使用
> 详见[Stack Overflow](http://stackoverflow.com/questions/24158648/why-isnt-projectname-prefix-pch-created-automatically-in-xcode-6)