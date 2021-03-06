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
4. 在Build Settings 搜索 Other Linker Flags , 添加 -force_load+空格+(微信sdk／银联sdk)控件路径，如： （-force_load $(PROJECT_DIR)/testSDK/agreeUPPay/libPaymentControl.a）。
5.需要在 `Info.plist` 添加如下代码：
```
	<key>NSAppTransportSecurity</key>
	<dict>
		<key>NSAllowsArbitraryLoads</key>
		<true/>
	</dict>
	
	<key>LSApplicationQueriesSchemes</key>
	<array>
		<string>weixin</string>
		<string>weixin://</string>
		<string>wechat</string>
	</array>
```
## SDK使用
1. 在需要使用支付的页面引入头文件
```
#import "AgreePay.h"
```
2. 调起支付函数

支付宝支付代码如下：
```
//后端返回数据：
//{
//    message = "\U6210\U529f";
//    respCode = 000000;
//    result =     {
//        TrxNo = ".......";
//        credential = "sign=.....";
//        payChannel = ALI;
//    };
//}
//调起支付
[AgreePay payOrder:result appURLScheme:@"支付宝 URL Schemes" withCompletion:^(NSString *agreeResult) {

	/*
	返回8100 支付成功
	返回8104 支付失败
	返回8101 退出支付
	返回8111 异常返回
	处理支付宝网页支付回调
	
	 */
	NSLog(@"%@",agreeResult);

    }];
```

微信支付代码如下：
```
//后端返回数据：
//{
//    message = "\U6210\U529f";
//    respCode = 000000;
//    result =     {
//        TrxNo = ".......";
//        credential = ".......";
//       payChannel = WEIXIN;
//    };
//}
//调起支付
[AgreePay payOrder:result appURLScheme:@"微信 URL Schemes" withCompletion:^(NSString *agreeResult) {

//      如果没有安装微信，会返回“没有安装微信app”
	NSLog(@"%@",agreeResult);
    }];
```

银联控件支付代码如下：
```
//后端返回数据：
//{
//    message = "\U6210\U529f";
//    respCode = 000000;
//   result =     {
//        TrxNo = ".......";
//        credential = ".......";
//        payChannel = ACP;
//   };
//}
//调起支付
ViewController * __weak weakSelf = self;
[AgreePay payOrder:result viewController:weakSelf appURLScheme:@"agreeUPPay1" appmodel:mode withCompletion:^(NSString *agreeResult) {    
                    }];
//mode "00"代表接入生产环境（正式版本需要）；"01"代表接入开发测试环境（测试版本需要）；
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
	返回8111 异常返回
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
	返回8111 异常返回
	 */
    }];
    return YES;
}
```
