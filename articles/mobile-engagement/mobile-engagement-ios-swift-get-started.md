---
title: "适用于 iOS (Swift) 的 Azure Mobile Engagement 入门 | Microsoft Docs"
description: "了解如何使用适用于 iOS 应用的具有分析和推送通知功能的 Azure Mobile Engagement。"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1011b9823333e79a52cd2d187df4f8d063b1f799


---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>适用于 Swift 中 iOS 应用的 Azure Mobile Engagement 入门
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，并向 iOS 应用程序的细分用户发送推送通知。
在本教程中，你将创建一个空白 iOS 应用，它使用 Apple Push Notification 系统 (APNS) 接收推送通知。

本教程需要的内容如下：

* XCode 8，你可以从 MAC 应用商店进行安装
* [Mobile Engagement iOS SDK]
* 推送通知证书 (.p12)，你可以从 Apple 开发中心获取

> [!NOTE]
> 本教程使用 Swift 3.0 版。 
> 
> 

完成本教程是学习有关 iOS 应用的所有其他 Mobile Engagement 教程的先决条件。

> [!NOTE]
> 若要完成本教程，你必须有一个有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started)。
> 
> 

## <a name="a-idsetupazmeasetup-mobile-engagement-for-your-ios-app"></a><a id="setup-azme"></a>为 iOS 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnectingappaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端
本教程提供的“基本集成”是收集数据和发送推送通知的最低要求。 在“ [Mobile Engagement iOS SDK 集成](mobile-engagement-ios-sdk-overview.md)

我们将通过 XCode 创建基本应用，以演示该集成：

### <a name="create-a-new-ios-project"></a>创建新的 iOS 项目
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>将应用连接到 Mobile Engagement 后端
1. 下载 [Mobile Engagement iOS SDK]
2. 将 .tar.gz 文件解压缩到计算机中的文件夹中
3. 右键单击该项目，然后选择“将文件添加到 ...”
   
    ![][1]
4. 导航到在其中解压缩 SDK 的文件夹，选择 `EngagementSDK` 文件夹，然后按“确定”。
   
    ![][2]
5. 打开“`Build Phases`”选项卡并在“`Link Binary With Libraries`”菜单中添加框架，如下所示：
   
    ![][3]
6. 创建桥接标头，以便能够使用 SDK 的 Objective C API，方法是选择“文件”>“新建”>“文件”>“iOS”>“源”>“标头文件”。
   
    ![][4]
7. 编辑桥接标头文件，以向 Swift 代码公开 Mobile Engagement Objective-C 代码，并添加下列导入︰
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. 在“构建设置”下，确保“Swift 编译器 - 代码生成”下的 Objective-C 桥接标头具有此标头的路径。 下面是一个路径示例︰ **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h（具体取决于路径）**
   
   ![][6]
9. 返回到 Azure 门，在应用的“ *连接信息* ”页中复制连接字符串
   
   ![][5]
10. 现在将连接字符串粘贴在 `didFinishLaunchingWithOptions` 代理中
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a name="a-idmonitoraenabling-realtime-monitoring"></a><a id="monitor"></a>启用实时监视
为了开始发送数据并确保用户处于活动状态，必须将至少一个屏幕（活动）发送到 Mobile Engagement 后端。

1. 打开 **ViewController.swift** 文件并将 **ViewController** 的基类替换为 **EngagementViewController**：
   
    `class ViewController : EngagementViewController {`

## <a name="a-idmonitoraconnect-app-with-realtime-monitoring"></a><a id="monitor"></a>将应用与实时监视相连接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegratepushaenabling-push-notifications-and-inapp-messaging"></a><a id="integrate-push"></a>启用推送通知和应用内消息传送
在市场推广活动中，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与 REACH 模块和用户进行交互。 在 Mobile Engagement 门户中，此模块称为 REACH。
以下各部分将介绍如何设置应用以接收推送通知。

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>使应用程序能够接收无提示的推送通知
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>将 Reach 库添加到你的项目
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

### <a name="modify-your-application-delegate"></a>修改你的应用程序代理
1. 在 `didFinishLaunchingWithOptions` 中，创建一个市场宣传模块并将其传递到现有的 Engagement 初始化行：
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>启用应用接收 APNS 推送通知
1. 将以下行添加到 `didFinishLaunchingWithOptions` 方法中：
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. 如下所示添加 `didRegisterForRemoteNotificationsWithDeviceToken` 方法：
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. 如下所示添加 `didReceiveRemoteNotification:fetchCompletionHandler:` 方法：
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png



<!--HONumber=Nov16_HO2-->


