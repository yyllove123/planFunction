planFunction
============

调研任务

###桌面快捷方式
看到百度地图有一个功能，可以把一个信息，在桌面上添加一个图标（快捷方式），调研它是怎么实现的。

######调查

原生应用不能直接在主屏幕上面添加快捷方式，只能通过safari的分享功能，在html里加入如下代码，safari就能识别到此页面能添加到主屏幕快捷方式：

	<link rel="apple-touch-icon" href="touch-icon-iphone.png">
	<link rel="apple-touch-icon" sizes="76x76" href="touch-icon-ipad.png">
	<link rel="apple-touch-icon" sizes="120x120" href="touch-icon-iphone-retina.png">
	<link rel="apple-touch-icon" sizes="152x152" href="touch-icon-ipad-retina.png">
	
######怎么用
可以仿造百度地图，它通过跳转到safari中，提示用户可以将此应用添加到主屏幕，点击快捷方式，则在web网页中又直接跳转到nativeApp里。

######参考资料
[Configuring Web Applications](https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html)

###通知栏控件如何实现
苹果ios8.0系统提供了通知栏可以实现控件的功能，原生的iMessage已经有此功能，调研如何实现。

######调查

苹果IOS8现在注册通知时需要多传一个categories参数，这个参数需要一个UIUserNotificationCategory对象的集合（NSSet），这个集合中描述了所有通知下发时的动作(UIUserNotificationAction)。

每个Action都需要有一个ID

	NSString * const notificationActionId  = @"actionId";
	NSString * const notificationCategoryId = @"categoryId";
	
创建一个Action对象

	UIMutableUserNotificationAction *action;
	action = [UIMutableUserNotificationAction new];
	[action setActivationMode:UIUserNotificationActivationModeBackground];
	[action setTitle:@"Action"];
	[action setIdentifier:notificationActionId];
	[action setDestructive:NO];
	[action setAuthenticationRequired:NO];
	
生成Category对象

	UIMutableUserNotificationCategory *actionCategory;
    actionCategory = [UIMutableUserNotificationCategory new];
    [actionCategory setIdentifier:notificationCategoryId];
    [actionCategory setActions:@[action] forContext:UIUserNotificationActionContextDefault];
    
注册通知

	NSSet *categories = [NSSet setWithObject:actionCategory];
	UIUserNotificationType types = (UIUserNotificationTypeAlert | UIUserNotificationTypeSound | UIUserNotificationTypeBadge);
	UIUserNotificationSettings *settings;
	settings = [UIUserNotificationSettings settingsForTypes:types categories:categories];
    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    
服务器端发送推送时的json格式

	"aps" : { 
	    "alert"    : "Hello World",
	    "category" : "categoryId"  // 对应上面的categoryId
	}
	
客户端接收，需事先代理方法

	// Called when your app has been activated by the user selecting an action from a local notification.
	// A nil action identifier indicates the default action.
	// You should call the completion handler as soon as you've finished handling the action.
	
	- (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void(^)())completionHandler NS_AVAILABLE_IOS(8_0);
	
	// Called when your app has been activated by the user selecting an action from a remote notification.
	// A nil action identifier indicates the default action.
	// You should call the completion handler as soon as you've finished handling the action.
	
	- (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forRemoteNotification:(NSDictionary *)userInfo completionHandler:(void(^)())completionHandler NS_AVAILABLE_IOS(8_0);

判断action

	- (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forRemoteNotification:(NSDictionary *)userInfo completionHandler:(void (^)())completionHandler {
	  
	    if ([identifier isEqualToString:notificationActionId]) {
	  
	        NSLog(@"action");
	    }
	       
	    if (completionHandler) {
	  
	        completionHandler();
	    }
	}

######参考资料
[iOS 8创建交互式通知](http://www.cocoachina.com/ios/20141009/9857.html)

###安装程序
pp助手是如何实现，下载完程序直接安装的功能。

###Git忽略文件
[Git忽略文件方法](http://www.cnblogs.com/eddy-he/archive/2012/03/08/git_ignore_file.html)

[设置SVN,Git忽略MAC的.DS_Store文件的方法](http://bmanlog.sinaapp.com/?p=32)