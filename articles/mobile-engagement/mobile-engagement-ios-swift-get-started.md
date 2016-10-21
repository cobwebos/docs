<properties
	pageTitle="适用于 iOS ( Swift) 的 Azure Mobile Engagement 入门"
	description="了解如何使用适用于 iOS 应用的具有分析和推送通知功能的 Azure Mobile Engagement。"
	services="mobile-engagement"
	documentationCenter="ios"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="hero-article"
	ms.date="08/19/2016"
	ms.author="piyushjo" />

# 适用于 Swift 中 iOS 应用的 Azure Mobile Engagement 入门

[AZURE.INCLUDE [Hero 教程切换器](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，并向 iOS 应用程序的细分用户发送推送通知。在本教程中，你将创建一个空白 iOS 应用，它使用 Apple Push Notification 系统 (APNS) 接收推送通知。

本教程需要的内容如下：

+ XCode 6 或 XCode 7，你可以从 MAC 应用商店进行安装
+ [Mobile Engagement iOS SDK]
+ 推送通知证书 (.p12)，你可以从 Apple 开发中心获取

> [AZURE.NOTE] 本教程使用 Swift 2.0 版。

完成本教程是学习有关 iOS 应用的所有其他 Mobile Engagement 教程的先决条件。

> [AZURE.NOTE] 若要完成本教程，你必须有一个有效的 Azure 帐户。如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-CN%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started)。

##<a id="setup-azme"></a>为 iOS 应用设置 Mobile Engagement

[AZURE.INCLUDE [在门户中创建 Mobile Engagement 应用](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端

本教程提供的“基本集成”是收集数据和发送推送通知的最低要求。在“[Mobile Engagement iOS SDK 集成](mobile-engagement-ios-sdk-overview.md)”中可找到完整的集成文档

我们将通过 XCode 创建基本应用，以演示该集成：

###创建新的 iOS 项目

[AZURE.INCLUDE [创建新的 iOS 项目](../../includes/mobile-engagement-create-new-ios-app.md)]

###将应用连接到 Mobile Engagement 后端

1. 下载 [Mobile Engagement iOS SDK]
2. 将 .tar.gz 文件解压缩到计算机中的文件夹中
3. 右键单击该项目，然后选择“将文件添加到 ...”

	![][1]

4. 导航到在其中解压缩 SDK 的文件夹，选择 `EngagementSDK` 文件夹，然后按“确定”。

	![][2]

5. 打开“`Build Phases`”选项卡并在“`Link Binary With Libraries`”菜单中添加框架，如下所示。**注意**必须包括 `CoreLocation, CFNetwork, CoreTelephony, and SystemConfiguration`：

	![][3]

6. 对于 **XCode 7** - 添加 `libxml2.tbd` 而不是 `libxml2.dylib`。

7. 创建桥接标头，以便能够使用 SDK 的 Objective C API，方法是选择“文件”>“新建”>“文件”>“iOS”>“源”>“标头文件”。

	![][4]

8. 编辑桥接标头文件，以向 Swift 代码公开 Mobile Engagement Objective-C 代码，并添加下列导入︰

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

9. 在“构建设置”下，确保“Swift 编译器 - 代码生成”下的 Objective-C 桥接标头具有此标头的路径。下面是一个路径示例︰**$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h（具体取决于路径）**

	![][6]

10. 返回到 Azure 门，在应用的“*连接信息*”页中复制连接字符串

	![][5]

11. 现在将连接字符串粘贴在 `didFinishLaunchingWithOptions` 代理中

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>启用实时监视

为了开始发送数据并确保用户处于活动状态，必须将至少一个屏幕（活动）发送到 Mobile Engagement 后端。

1. 打开 **ViewController.swift** 文件并将 **ViewController** 的基类替换为 **EngagementViewController**：

	`class ViewController : EngagementViewController {`

##<a id="monitor"></a>将应用与实时监视相连接

[AZURE.INCLUDE [将应用与实时监视相连接](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>启用推送通知和应用内消息传送

在市场推广活动中，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与 REACH 模块和用户进行交互。在 Mobile Engagement 门户中，此模块称为 REACH。以下各部分将介绍如何设置应用以接收推送通知。

### 使应用程序能够接收无提示的推送通知

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### 将 Reach 库添加到你的项目

1. 右键单击你的项目
2. 选择 `Add file to ...`
3. 导航到在其中解压缩 SDK 的文件夹
4. 选择 `EngagementReach` 文件夹
5. 单击“添加”
6. 编辑桥接标头文件，以将 Mobile Engagement Objective-C Reach 标头公开，并添加下列导入︰

		/* Mobile Engagement Reach */
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEReachPollQuestion.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### 修改你的应用程序代理

1. 在 `didFinishLaunchingWithOptions` 中，创建一个 Reach 模块并将其传递到你现有的 Engagement 初始化行︰

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###启用应用接收 APNS 推送通知
1. 将以下行添加到 `didFinishLaunchingWithOptions` 方法中：

		/* Ask user to receive push notifications */
		if #available(iOS 8.0, *)
		{
		   let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
		   application.registerUserNotificationSettings(settings)
		   application.registerForRemoteNotifications()
		}
		else
		{
		   application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
		}

2. 如下所示添加 `didRegisterForRemoteNotificationsWithDeviceToken` 方法：

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. 如下所示添加 `didReceiveRemoteNotification:fetchCompletionHandler:` 方法：

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png

<!---HONumber=AcomDC_0921_2016-->