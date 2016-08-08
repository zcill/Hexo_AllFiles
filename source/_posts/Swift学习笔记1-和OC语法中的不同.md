title: Swift学习笔记1-和OC语法中的不同
date: 2016-02-10 20:03:59
tags: Swift
---
好久没写博客了，过年回家之后就不像在上海一个人呆着那样会每时每刻敲代码。家里事还是挺多了，快毕业了，就要开始被当做社会人来看待了，逢年过节已经要开始跑亲戚串门了。

**今天抽了点时间，把自己学习Swift中感觉和Objective-C不一样的地方，大概列出来，供自己以后学习回顾方便。**

### 流程控制
Swift中流程控制主要是`for-in` `switch`和Objective-C有比较大的区别，其他`while` `do-while`和OC可以说一样的。

#### for-in
⭐️**Objective－C**

我们现在需要打印5句话，需要定义一个i，写清楚条件才可以：

```
for (int i = 0; i < 5; i++) {
	NSLog("You are the apple of my eye");
}
```

⭐️**Swift**

如果是Swift，可以直接用for in来代替：

```

// ... 是Swift中的闭区间
// 1...5表示的意义是闭区间[1, 5]，说明1和5都包含

for i in (1...5) {
	print("You are the apple of my eye")
}
```

再说一个Swift语法中的一个特殊的东西`_`，没错，这个下划线可以用来代替一些一次性的变量或者常量，像上面的代码，其实我们并不需要定义这个i，因为在循环中我们并没有使用到他。在Swift中可以简化成这样:

```
for _ in (1...5) {
	print("You are the apple of my eye")
}
```
#### switch
⭐️**Objective－C**

switch在C语言中，固定格式是要求使用`switch ()`，括号中需要填入的一般是整型值，C语言中的话，使用Boolean也可以。

```
// C语言中
    switch (i) {
        case 1:
            break;
        case 2:
            break;
        case 3:
            break;
        default:
            break;
    }
```
⭐️**Swift**

在Swift中，switch要强大不少
##### 格式上
不需要`( )`和`break`

```
switch i {
case 1:
    print("111")
    
case 2:
    print("222")
    
case 3:
    print("333")
    
default:
    print("default")
}
```
不需要使用break去让他跳出这个switch，他运行完case之后会自动跳出。如果需要让他运行完case1之后接着运行case2我们需要在case中加入fallthrough就可以了，不过这样也是让他执行下一个case不能执行后面全部的case的:

```
	case 1:
		print("111")
		fallthrough
	case 2:
		print("222")
```
##### 对于condition的要求
不规定必须要整型，一些基本匹配要求他都可以满足，例如元组:

```
var Tom = (age:21, sex:"male")

switch Tom {
	
}
```
这样是完全可以的，使用元组内的元素内容去匹配case:

```
var Tom = (age:21, sex:"male")

switch Tom {
case (20, _):
    print("Tom's age is 20")
    
case (_, "female"):
    print("Tom's sex is female")
    
case let (age, sex):
    print("Tom's age is \(age), his sex is \(sex)")
}
```
这里注意，case1中使用`(20, _)`是为了让他匹配时忽略Tom的sex元素内容，无论是什么sex，只要age为20就进入这个case；同样case2也是一个道理，无论age为多少，sex只要为female就进入这个case

##### 对于case后的content
这里关注一下第三个case:

```
case let (age, sex):
	print("\(age) \(sex)")
```
这里的case后可以直接定义一个常量，用`let (age, sex)`来表示，这样的意思就是无论什么条件他都会进入这个case，类似于`(_, _)`，不一样的是，如果你在case中，需要使用元组中的元素内容，就可以使用let这种方法，不需要的话可以使用`(_, _)`