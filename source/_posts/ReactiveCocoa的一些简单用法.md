title: ReactiveCocoa的一些简单用法
date: 2016-02-22 12:07:57
tags: ReactiveCocoa
---
> 最近在学**Swift**，所以一直没能抽出时间去好好学一学**ReactiveCocoa**。昨天花了点时间，看了看RAC在github上的Documents，感觉可以把一些简单的基本功能拿出来在项目中尝试下。

### 使用`rac_signalForControlEvents`监听textField

```
    [[self.textField rac_signalForControlEvents:UIControlEventEditingChanged] subscribeNext:^(id x) {
        NSLog(@"value changed: %@", x);
    }];
```
打印

```
2016-02-22 16:02:53.374 LearnReactiveCocoa-Day2[6525:147034] value changed: <UITextField: 0x7fafa0414930; frame = (79 177; 217 30); text = '1'; clipsToBounds = YES; opaque = NO; autoresize = RM+BM; gestureRecognizers = <NSArray: 0x7fafa059dd60>; layer = <CALayer: 0x7fafa0414ef0>>
2016-02-22 16:02:53.661 LearnReactiveCocoa-Day2[6525:147034] value changed: <UITextField: 0x7fafa0414930; frame = (79 177; 217 30); text = '12'; clipsToBounds = YES; opaque = NO; autoresize = RM+BM; gestureRecognizers = <NSArray: 0x7fafa059dd60>; layer = <CALayer: 0x7fafa0414ef0>>
2016-02-22 16:02:54.018 LearnReactiveCocoa-Day2[6525:147034] value changed: <UITextField: 0x7fafa0414930; frame = (79 177; 217 30); text = '123'; clipsToBounds = YES; opaque = NO; autoresize = RM+BM; gestureRecognizers = <NSArray: 0x7fafa059dd60>; layer = <CALayer: 0x7fafa0414ef0>>
2016-02-22 16:02:55.043 LearnReactiveCocoa-Day2[6525:147034] value changed: <UITextField: 0x7fafa0414930; frame = (79 177; 217 30); text = '1234'; clipsToBounds = YES; opaque = NO; autoresize = RM+BM; gestureRecognizers = <NSArray: 0x7fafa059dd60>; layer = <CALayer: 0x7fafa0414ef0>> 
```

### 使用`rac_gestureSignal`监听gesture

```
    self.view.userInteractionEnabled = YES;
    
    UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] init];
    [[tap rac_gestureSignal] subscribeNext:^(id x) {
        NSLog(@"tap: %@", x);
    }];
    
    [self.view addGestureRecognizer:tap];
```
打印

```
2016-02-22 16:06:16.576 LearnReactiveCocoa-Day2[6558:149669] tap: <UITapGestureRecognizer: 0x7fb312d94560; state = Ended; view = <UIView 0x7fb312d869d0>; target= <(action=sendNext:, target=<RACPassthroughSubscriber 0x7fb312d95710>)>>
2016-02-22 16:06:17.324 LearnReactiveCocoa-Day2[6558:149669] tap: <UITapGestureRecognizer: 0x7fb312d94560; state = Ended; view = <UIView 0x7fb312d869d0>; target= <(action=sendNext:, target=<RACPassthroughSubscriber 0x7fb312d95710>)>>
2016-02-22 16:06:17.901 LearnReactiveCocoa-Day2[6558:149669] tap: <UITapGestureRecognizer: 0x7fb312d94560; state = Ended; view = <UIView 0x7fb312d869d0>; target= <(action=sendNext:, target=<RACPassthroughSubscriber 0x7fb312d95710>)>>
2016-02-22 16:06:19.398 LearnReactiveCocoa-Day2[6558:149669] tap: <UITapGestureRecognizer: 0x7fb312d94560; state = Ended; view = <UIView 0x7fb312d869d0>; target= <(action=sendNext:, target=<RACPassthroughSubscriber 0x7fb312d95710>)>>
```

### 使用`rac_addObserverForName`监听notification

**这里监听的通知是`UIApplicationDidEnterBackgroundNotification`这个通知，就是App进入后台会调用这个通知**

```
    [[[NSNotificationCenter defaultCenter] rac_addObserverForName:UIApplicationDidEnterBackgroundNotification object:nil] subscribeNext:^(id x) {
        NSLog(@"notification: %@", x);
    }];
```
打印

```
2016-02-22 16:07:52.051 LearnReactiveCocoa-Day2[6586:150865] notification: NSConcreteNotification 0x7f839a76e1d0 {name = UIApplicationDidEnterBackgroundNotification; object = <UIApplication: 0x7f839a600840>}
2016-02-22 16:07:58.775 LearnReactiveCocoa-Day2[6586:150865] notification: NSConcreteNotification 0x7f839a43d260 {name = UIApplicationDidEnterBackgroundNotification; object = <UIApplication: 0x7f839a600840>}
```

### 使用`RACScheduler`类使用计时器
**延迟一秒后运行一段代码**

```
    [[RACScheduler mainThreadScheduler] afterDelay:1 schedule:^{
        NSLog(@"1 second ago");
    }];
```

**每个相同的时间间隔运行一段代码，可用来做秒表**

```
    [[RACSignal interval:1 onScheduler:[RACScheduler mainThreadScheduler]] subscribeNext:^(id x) {
        NSLog(@"per 1 second");
    }];
```

### 可以用来取代返回值为`void`的代理方法

```
    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"RAC" message:@"ReactiveCocoa" delegate:self cancelButtonTitle:@"Cancel" otherButtonTitles:@"Ensure", nil];
    
    [[alertView rac_buttonClickedSignal] subscribeNext:^(id x) {
        NSLog(@"%@", x);
    }];
     
    [alertView show];
```

### 使用`RACObserve`实现KVO，监听一个keyPath
我们先来写一个`scrollView`，大概写下他的`frame`

```
    UIScrollView * scrollView = [[UIScrollView alloc]initWithFrame:self.view.bounds];
    scrollView.delegate = (id<UIScrollViewDelegate>)self;
    scrollView.contentSize = CGSizeMake(self.view.frame.size.width * 3, self.view.frame.size.height * 2);
    scrollView.backgroundColor = [UIColor colorWithRed:0.400 green:1.000 blue:1.000 alpha:1.000];
    [self.view addSubview:scrollView];
```
只需要用`RACObserve`就可以监听他的`contentOffset`属性的值
    
``` 
    [RACObserve(scrollView, contentOffset) subscribeNext:^(id x) {
        NSLog(@"contentOffSet: %@", x);
    }];
```