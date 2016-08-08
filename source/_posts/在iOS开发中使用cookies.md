title: 在iOS开发中使用cookies
date: 2016-04-20 16:09:28
tags: iOS开发
---
最近在做毕业设计，大概是一个学生会招聘的App吧。用到了关于cookies的内容，去查了点资料，准备记录下来。

## 登录
登录模块，经过分析，学校的教务在线使用的虽然和正常的登录方式不太一样，但是还都是通过发送post请求实现的。

抓包发现，发送post请求的同时，需要携带这几个参数:

```
NSDictionary *params = @{
            @"__EVENTTARGET":@"",
            @"__LASTFOCUS":@"",
            @"__VIEWSTATE":@"...",
            @"__EVENTVALIDATION":@"...",
            @"rblUserType":@"Student",
            @"ddlCollege":@"180     ",
            @"StuNum":@"$$$",
            @"TeaNum":@"",
            @"Password":@"$$$",
            @"login":@"登录"
    };
```
其中，`__EVENTTARGET`参数，如果是登录请求，就为空；如果是退出登录的请求，就是`lbtExit`

直接发送post请求就可以实现登录操作，而且AF会自动保存cookies，下次登录也是保持着登录的状态。

```
[manager POST:@"http://jwc.jxnu.edu.cn/Default_Login.aspx?preurl=" 
parameters:params 
success:^(NSURLSessionDataTask *task, id responseObject) {

        NSString *string = [[NSString alloc] initWithData:responseObject encoding:NSUTF8StringEncoding];
        NSLog(@"success! task: %@, responseObject: %@", task, responseObject);
        NSLog(@"data: %@", string);

} failure:^(NSURLSessionDataTask *task, NSError *error) {
        NSLog(@"failure! task: %@, error: %@", task, error.description);
}];
```

## 退出登录
使用`AFNetworking`发送post请求，AF会自动保存cookies，保持一个登录的状态，所以在我一直向服务器发送退出登录的请求都error了。

所以我们这里就使用清除cookies的操作来实现退出登录。

### `NSHTTPCookieStorage`
iOS的SDK中有一个`NSHTTPCookieStorage`类，看名字就知道是一个保存cookie的地方，应该是一个单例。

```
NSHTTPCookieStorage *cookieJar = [NSHTTPCookieStorage sharedHTTPCookieStorage];
```
我们这里获取到了这个单例，把学校教务在线的Domain作为参数使用`cookiesForURL:`方法，就可以得到一个数组，这里拿到数组就可以归档实现保存本地。

清除cookie的话，在遍历数组的时候使用`deleteCookie:`的方法，就可以把cookie全部清掉，实现我们想要的清除cookie的操作。

```
NSArray *cookies = [cookieJar cookiesForURL: [NSURL URLWithString:@"http://jwc.jxnu.edu.cn"]];
    
for (NSHTTPCookie *cookie in cookies) {
	NSLog(@"cookieName: %@", [cookie name]);
	[cookieJar deleteCookie:cookie];
}
    
NSLog(@"cookies: %@", cookies);
```