title: LocalAuthentication源码学习
date: 2016-02-29 18:39:11
tags: iOS
---
截止到现在，已经很多App都包含使用TouchID代替登录密码验证用户身份。在我们iOS开发中，其实加入一个TouchID验证的模块是很简单的。为了深入了解苹果这个framework的内容，我准备点开仔细看看源码。因为不是开源的，仅仅是看看苹果暴露出来的接口，提高自己的学习能力而已。
#### 导入`LocalAuthentication.framework`框架
我们点开这个framework，发现里面主要有这么几个东西

![](http://7xq5gj.com1.z0.glb.clouddn.com/blog/localAuthentication/LocalAuthenticationIndex.png)

	- LAContext.h
	- LAError.h
	- LAPublicDefines.h
	- LocalAuthentication.h

## - LocalAuthentication.h
这个没什么可讲的吧，代码就两行，一行导入`LAContext.h`,一行导入`LAError.h`，这个`LocalAuthentication`类是暴露出来方便开发者调用的类。

## - LAPublicDefines.h
先从简单的开始讲吧，首先是`LAPublicDefines.h`，从名字上来看是公共宏定义类，里面包含了许多定义好的宏，这些宏会在`LAContext.h`得到使用。
![](http://7xq5gj.com1.z0.glb.clouddn.com/blog/localAuthentication/LAPublicDefines.png)


#### Policies

```
// Policies
#define kLAPolicyDeviceOwnerAuthenticationWithBiometrics    1
#define kLAPolicyDeviceOwnerAuthentication                  2
```

#### Options

```
// Options
#define kLAOptionUserFallback                               1
#define kLAOptionAuthenticationReason                       2
```

#### Error codes

`Error codes`就是错误代码了，提示你输入touchID后，如果认证不正确，会返回一个错误代码，通过错误代码返回给用户错误信息。

```
// Error codes
#define kLAErrorAuthenticationFailed                       -1
#define kLAErrorUserCancel                                 -2
#define kLAErrorUserFallback                               -3
#define kLAErrorSystemCancel                               -4
#define kLAErrorPasscodeNotSet                             -5
#define kLAErrorTouchIDNotAvailable                        -6
#define kLAErrorTouchIDNotEnrolled                         -7
#define kLAErrorTouchIDLockout                             -8
#define kLAErrorAppCancel                                  -9
#define kLAErrorInvalidContext                            -10
```

## - LAError.h
这个类其实也不用赘述，就是一个枚举，里面写的是错误的类型，其实就是把上面的`kLAError`宏写进这个枚举了，具体代码注释写的很清晰，大概翻译了一下

```
typedef NS_ENUM(NSInteger, LAError)
{
    LAErrorAuthenticationFailed, 	// 验证信息出错，就是说你指纹不对
    LAErrorUserCancel           	// 用户取消了验证
    LAErrorUserFallback         	// 用户点击了手动输入密码的按钮，所以被取消了
    LAErrorSystemCancel         	// 被系统取消，就是说你现在进入别的应用了，不在刚刚那个页面，所以没法验证
    LAErrorPasscodeNotSet       	// 用户没有设置TouchID
    LAErrorTouchIDNotAvailable  	// 用户设备不支持TouchID
    LAErrorTouchIDNotEnrolled   	// 用户没有设置手指指纹
    LAErrorTouchIDLockout       	// 用户错误次数太多，现在被锁住了
    LAErrorAppCancel            	// 在验证中被其他app中断
    LAErrorInvalidContext   		// 请求验证出错
} NS_ENUM_AVAILABLE(10_10, 8_0);
```

## - LAContext.h
重头戏来了，想在自己的项目中使用TouchID，就要用到`LAContext`这个类里面的方法
首先映入眼帘的是一个NS_ENUM枚举`LAPolicy`。

> 第一个枚举`LAPolicyDeviceOwnerAuthenticationWithBiometrics `就是说，用的是手指指纹去验证的；
> 
> 第二个枚举`LAPolicyDeviceOwnerAuthentication`少了`WithBiometrics`则是使用TouchID或者密码验证

```
typedef NS_ENUM(NSInteger, LAPolicy)
{
    LAPolicyDeviceOwnerAuthenticationWithBiometrics,
    LAPolicyDeviceOwnerAuthentication

} NS_ENUM_AVAILABLE(10_10, 8_0);
```

首先暴露出来的几个方法，注意这里都是实例方法，所以需要创建一个实例对象去才能调用，使用
`LAContext *context = [LAContext alloc] init];`创建一个LAContext对象。

> `canEvaluatePolicy:error:`方法用来检查当前设备是否可用touchID，返回一个BOOL值
> 
> `evaluatePolicy:localizedReason:reply:`调用验证方法，注意这里的三个参数：
> 
> 第一个参数`policy`是要使用上面那个`LAPolicy`的枚举
> 
> 第二个参数`localizedReason`是`NSString`类型的验证理由
> 
> 第三个参数`reply`则是一个回调Block，block内有一个BOOL类型的success判断是否成功验证，还有一个用于判断错误信息的`NSError`类型的error
> 
> `invalidate`方法用来废止这个`context`

```
- (BOOL)canEvaluatePolicy:(LAPolicy)policy error:(NSError * __autoreleasing *)error __attribute__((swift_error(none)));

- (void)evaluatePolicy:(LAPolicy)policy
       localizedReason:(NSString *)localizedReason
                 reply:(void(^)(BOOL success, NSError * __nullable error))reply;

- (void)invalidate;

```

枚举`LACredentialType``LAAccessControlOperation `，这个东西和下面的几个方法我查了很久也没弄明白用在哪，苹果官方文档也看的不太懂，枚举中只有一个`LACredentialTypeApplicationPassword`。

不过通过这个`NS_ENUM_AVAILABLE(10_11, 9_0)`还有方法后面的`NS_AVAILABLE(10_11, 9_0)`知道这个枚举和这两个方法只能在OS X 10.11和iOS 9.0以上版本使用，所以可能是比较新的东西，后面苹果还会对他扩充吧。

可能后面会去请教一些大神把这个东西大概理解一下。

```
typedef NS_ENUM(NSInteger, LACredentialType)
{
    LACredentialTypeApplicationPassword = 0,
} NS_ENUM_AVAILABLE(10_11, 9_0);
```


```
- (BOOL)setCredential:(nullable NSData *)credential
                type:(LACredentialType)type;
- (BOOL)isCredentialSet:(LACredentialType)type;
```

```
typedef NS_ENUM(NSInteger, LAAccessControlOperation)
{
    /// Access control will be used for item creation.
    LAAccessControlOperationCreateItem,

    /// Access control will be used for accessing existing item.
    LAAccessControlOperationUseItem,

    /// Access control will be used for key creation.
    LAAccessControlOperationCreateKey,

    /// Access control will be used for accessing existing key.
    LAAccessControlOperationUseKeySign
} NS_ENUM_AVAILABLE(10_11, 9_0);
```

```
- (void)evaluateAccessControl:(SecAccessControlRef)accessControl
                    operation:(LAAccessControlOperation)operation
              localizedReason:(NSString *)localizedReason
                        reply:(void(^)(BOOL success, NSError * __nullable error))reply
                        NS_AVAILABLE(10_11, 9_0);
```
属性的话，这里有四个。
> `localizedFallbackTitle` 可以设置验证TouchID时弹出Alert的输入密码按钮的标题
> 
> `maxBiometryFailures` 最大指纹尝试错误次数。 这个属性我们可以看到他后面写了`NS_DEPRECATED_IOS(8_3, 9_0)`，说明这个属性在iOS 8.3被引入，在iOS 9.0被废弃，所以如果系统版本高于9.0是无法使用的。
> 
> `evalueatedPolicyDomainState` 这个可能和设备的指纹设置中，你录入的指纹有关，暂时没弄太明白
> 
> `touchIDAuthenticationAllowableReuseDuration` 这个属性应该是类似于支付宝的指纹开启应用，如果你打开他解锁之后，按Home键返回桌面，再次进入支付宝是不需要录入指纹的。因为这个属性可以设置一个时间间隔，在时间间隔内是不需要再次录入。默认是0秒，最长可以设置5分钟。

```
@property (nonatomic, nullable, copy) NSString *localizedFallbackTitle;

@property (nonatomic, nullable) NSNumber *maxBiometryFailures NS_DEPRECATED_IOS(8_3, 9_0);

@property (nonatomic, nullable, readonly) NSData *evaluatedPolicyDomainState NS_AVAILABLE(10_11, 9_0);

@property (nonatomic) NSTimeInterval touchIDAuthenticationAllowableReuseDuration NS_AVAILABLE_IOS(9_0);
```