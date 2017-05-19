# Agree iOS SDK

## 简介
当前版本支持，微信支付、支付宝支付、银联控件支付三种支付方式。
## 版本要求
iOS SDK 要求 iOS 7.0 及以上版本 
## 导入
1. 获取SDK
	下载SDK
2. 依赖 Frameworks:
	必须:
```
	libz.tbd
	libc++.tbd
	libsqlite3.0.tbd
	libstdc++.tbd
	CFNetwork.framework
	CoreMotion.framework
	Security.framework
```
3. 添加 URL Schemes : 在 Xcode 中，选择你的工程设置项，选中 “TARGETS” 一栏，在 “Info” 标签栏的 “URL Types” 添加 “URL Schemes” ，分别添加支付宝，微信，银联 “URL Schemes”，建议名字起复杂一些，避免与其他程序冲突。（注意：微信的 URL Schemes 需要填入微信支付app id）
4. 在Build Settings 搜索 Other Linker Flags , 添加 -force_load+空格+控件路径，如： （-force_load $(PROJECT_DIR)/testSDK/agreeUPPay/libPaymentControl.a）。
5. 如果 App 需要访问 `http://`, 需要在 `Info.plist` 添加如下代码：
```
	<key>NSAppTransportSecurity</key>
	<dict>
		<key>NSAllowsArbitraryLoads</key>
		<true/>
	</dict>
```
## SDK使用
1. 在需要使用支付的页面引入头文件
```
#import "AgreePay.h"
```
2. 调起支付函数

支付宝支付代码如下：
```
//写在点击支付宝支付的函数中
NSMutableDictionary *mutdic=[NSMutableDictionary dictionaryWithCapacity:0];
mutdic[@"payOrder"] = @"后端返回";
//sdk判断支付宝支付类型，使用agreeAlipay
mutdic[@"payType"] = @"agreeAlipay";
//调起支付
[AgreePay payOrder:mutdic appURLScheme:@"支付宝 URL Schemes" withCompletion:^(NSString *agreeResult) {
    }];
```

微信支付代码如下：
```
//写在点击微信支付的函数中
NSMutableDictionary *mutdic=[NSMutableDictionary dictionaryWithCapacity:0];
//以下参数通过后端返回
/** appid */
mutdic[@"appid"] = @"后端返回";
/** 商家向财付通申请的商家id */
mutdic[@"partnerid"] = @"后端返回";
/** 预支付订单 */
mutdic[@"prepayid"] = @"后端返回";
/** 商家根据财付通文档填写的数据和签名 */
mutdic[@"package"] = @"后端返回";
/** 随机串，防重发 */
mutdic[@"noncestr"] = @"后端返回";
/** 时间戳，防重发 */
mutdic[@"timestamp"] = @"后端返回";
/** 商家根据微信开放平台文档对数据做的签名 */
mutdic[@"sign"] = @"后端返回";
//sdk判断微信支付类型，使用agreePay
mutdic[@"payType"] = @"agreePay";
//调起支付
[AgreePay payOrder:mutdic appURLScheme:@"微信 URL Schemes" withCompletion:^(NSString *agreeResult) {
    }];
```

银联控件支付代码如下：
```
//写在点击银联控件支付的函数中
NSMutableDictionary *mutdic=[NSMutableDictionary dictionaryWithCapacity:0];
mutdic[@"tn"] = @"后端返回";
//sdk判断银联控件支付类型，使用agreeUPPay
mutdic[@"payType"] = @"agreeUPPay";
//调起支付
[AgreePay payOrder:mutdic appURLScheme:@"银联 URL Schemes" withCompletion:^(NSString *agreeResult) {
    }];
```

3. 支付结果回调
在AppDelegate.m 引入头文件
```
#import "AgreePay.h"
```

```
// iOS 8 及以下请用这个
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
    [AgreePay AgreeOpenURL:url withCompletion:^(NSString *agreeResult) {
	NSLog(@"result = %@",agreeResult);
	/*
	返回8100 支付成功
	返回8104 支付失败
	返回8101 退出支付
	 */
    }];
    return YES;
}
// iOS 9 及以上请用这个
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString*, id> *)options {
    [AgreePay AgreeOpenURL:url withCompletion:^(NSString *agreeResult) {
    NSLog(@"result = %@",agreeResult);
    /*
	返回8100 支付成功
	返回8104 支付失败
	返回8101 退出支付
	 */
    }];
    return YES;
}
```
