title: LearnReactiveCocoa-Day1
date: 2016-01-25 23:44:04
tags: ReactiveCocoa
---
<div class="github-widget" data-repo="ReactiveCocoa/ReactiveCocoa"></div>

	ReactiveCocoa，每一个中高级iOS开发者都应该会用的框架。

### ReactiveCocoa学习笔记-1
#### 为啥要学RAC
因为同事[@70kg](http://70kg.info)经常和我提RxJava😂，所以就准备去看看iOS中的函数式响应型编程ReactiveCocoa框架，毕竟这是这个是一个高端技术，学习门槛有点大，用起来逼格高，效果也很显著，有效提高了代码的质量、减少了冗余的垃圾代码。

#### 啥是函数式编程、响应型编程
> 参考这篇[最快让你上手ReactiveCocoa之基础篇-简书](http://www.jianshu.com/p/87ef6720a096)，用代码实例去说明响应式、函数式编程的理解

##### 响应式编程(Reactive Programming)

先用一段C语言代码

```
	int a = 1;
	int b = 2;
	int c = a + b;
	a++;
	printf("%d", c);
```
这里打印出来c是3，就算a在改变，c是不会变化的，这叫面向过程，一步一步实现的。响应式就是一触即响应，只要a有变化，就会带着与他有关系的c一起变化。在iOS中类似的就是KVO。

##### 函数式编程(Functional Programming)
函数式就是以函数为主，就是把操作都写成函数去嵌套调用，类似于链式，一层一层、一环一环去调用。

#### 使用ReactiveCocoa

##### 导入RAC
一般是三种方法

	方法一 Carthage导入
	方法二 CocoaPods导入
	方法三 手动拖入
	
##### 使用基本方法
> 因为zcill是菜鸡，所以参考的是gitbook上的一篇比较不错的教程翻译版[iOS的函数响应型编程](https://kevinhm.gitbooks.io/functionalreactiveprogrammingonios)

--
> 流和序列

```
	NSArray *array = @[@1, @2, @3];
    
	把数组化成一个流stream，注意流不能包含nil
	RACSequence *stream = [array rac_sequence];
	
	打印数组
	NSLog(@"sequence ---> %@", [stream array]);
```
这里可以把两个方法合并一起

```
	合并上面两个方法，先把array转化成流，再把流中每个元素的平方转化成数组打印出来
	NSLog(@"sequence ---> %@", [[[array rac_sequence] map:^id(id value) {
		return @(pow([value integerValue], 2));
	}] array]);
```
可以使用filter过滤操作

```
	过滤数组，判断每个元素与2求余是否为0
	NSLog(@"filter ---> %@", [[[array rac_sequence] filter:^BOOL(id value) {
		return [value integerValue] % 2 == 0;
	}] array]);
```
使用folding把流合并为单个值，注意左折叠和右折叠

```
	把一个序列流合并为单个值 folding
	NSLog(@"folding ---> %@", [[[array rac_sequence] map:^id(id value) {
		return [value stringValue];
        
		这里foldLeft是左折叠，表示从头到尾遍历数组，反之则是右折叠
	}] foldLeftWithStart:@"" reduce:^id(id accumulator, id value) {
		return [accumulator stringByAppendingString:value];
	}]);
```
--
> 信号

信号是RAC的核心组件之一。

订阅一个信号就类似于监听一个通知一样，而且他是一触即发的哦。

我们给textField订阅一个信号

```
    [self.textField.rac_textSignal subscribeNext:^(id x) {
        NSLog(@"New Value: %@", x);
    } error:^(NSError *error) {
        NSLog(@"Error: %@", error);
    } completed:^{
        NSLog(@"Completed");
    }];
```
你会发现，textField中的内容每有变化，控制台都会打印出东西来。
不过，错误的回调一般是不会出现的，成功也只是在释放的时候会调用一次。
所以，我们的代码可以简化成这样:

```
    [self.textField.rac_textSignal subscribeNext:^(id x) {
        NSLog(@"New Value: %@", x);
    }];
```
这样，只需要一行代码就可以实现对textField的监听，真是高效呢。

> 状态推导

状态推导也是RAC的核心组件之一。

写一个Button，如果textField里面的内容不是一个带@的邮箱，就不能点击按钮。

`RAC(...)`这个宏，需要两个参数：`对象`以及这个对象的某个属性`KeyPath`。然后将表达式右边的值和`keyPath`做一个单向的绑定，这个值必须是`NSObject`类型，所以我们会把`boolean`量封装成`NSNumber`。

```
	绑定信号，只允许textField中输入的内容包含@才可以enable这个button
	RAC(self.button, enabled) = [self.textField.rac_textSignal map:^id(id value) {
		return @([value rangeOfString:@"@"].location != NSNotFound);
	}];
```

重构代码，实现功能

```
// 绑定信号
    RACSignal *validEmailSignal = [self.textField.rac_textSignal map:^id(id value) {
        return @([value rangeOfString:@"@"].location != NSNotFound);
    }];
    
    // 设置button的enabled属性与validEmailSignal信号变化
    RAC(self.button, enabled) = validEmailSignal;
    
    // 设置textField的textColor根据validEmailSignal信号变化
    RAC(self.textField, textColor) = [validEmailSignal map:^id(id value) {
        if ([value boolValue]) {
            return [UIColor greenColor];
        } else {
            return [UIColor redColor];
        }
    }];
```
---
今天就学到这吧。

> 参考学习资料[iOS的函数响应型编程-GitBook](https://kevinhm.gitbooks.io/functionalreactiveprogrammingonios/)

> 代码可点击下面哦

<div class="github-widget" data-repo="zcill/LearnReactiveCocoa-Code"></div>