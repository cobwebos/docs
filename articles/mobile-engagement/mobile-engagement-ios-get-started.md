---
title: "适用于 iOS 的 Azure Mobile Engagement（Objective C）| Microsoft Docs"
description: "了解如何使用适用于 iOS 应用的具有分析和推送通知功能的 Azure Mobile Engagement。"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/17/2017
ms.author: piyushjo
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 1b87a2ebb35b31ee3d3139ecead6267e62eb1033
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>适用于 Objective C 中 iOS 应用的 Azure Mobile Engagement 入门
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，并向 iOS 应用程序的细分用户发送推送通知。
在本教程中，你将创建一个空白 iOS 应用，它使用 Apple Push Notification 系统 (APNS) 接收推送通知。

本教程需要的内容如下：

* XCode 8，你可以从 MAC 应用商店进行安装
* [Mobile Engagement iOS SDK]

完成本教程是学习有关 iOS 应用的所有其他 Mobile Engagement 教程的先决条件。

> [!NOTE]
> 若要完成本教程，你必须有一个有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started)。
>
>

## <a id="setup-azme"></a>为 iOS 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端
本教程提供的“基本集成”是收集数据和发送推送通知的最低要求。 在“ [Mobile Engagement iOS SDK 集成](mobile-engagement-ios-sdk-overview.md)

我们将通过 XCode 创建基本应用，以演示该集成。

### <a name="create-a-new-ios-project"></a>创建新的 iOS 项目
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>将应用连接到 Mobile Engagement 后端
1. 下载 [Mobile Engagement iOS SDK]。
2. 将 .tar.gz 文件解压缩到计算机中的一个文件夹中。
3. 右键单击该项目，然后选择“ **将文件添加到**”。

    ![][1]
4. 导航到 SDK 解压缩到的文件夹，选择 `EngagementSDK` 文件夹，单击左下角的“选项”，确保已选中“需要时复制项”和目标对应的复选框，然后按“确定”。

    ![][2]
5. 打开“构建阶段”选项卡，然后在“将二进制文件链接到库”菜单中添加框架，如下所示：

    ![][3]
6. 返回到 Azure 门户，在应用的“ **连接信息** ”页中复制连接字符串。

    ![][4]
7. 在 **AppDelegate.m** 文件中，添加以下代码行。

        #import "EngagementAgent.h"
8. 现在将连接字符串粘贴在 `didFinishLaunchingWithOptions` 代理中。

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
              [...]   
              [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
              [...]
        }
9. `setTestLogEnabled` 是一个可选语句，可启用使你能够确定问题的 SDK 日志。

## <a id="monitor"></a>启用实时监视
为了开始发送数据并确保用户处于活动状态，必须将至少一个屏幕（活动）发送到 Mobile Engagement 后端。

1. 打开 **ViewController.h** 文件并导入 **EngagementViewController.h**：

    `#import "EngagementViewController.h"`
2. 现在通过 `EngagementViewController` 替换 **ViewController** 接口的超类：

    `@interface ViewController : EngagementViewController`

## <a id="monitor"></a>将应用与实时监视相连接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>启用推送通知和应用内消息传送
在市场推广活动中，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与 REACH 模块和用户进行交互。 在 Mobile Engagement 门户中，此模块称为 REACH。
以下各部分将介绍如何设置应用以接收推送通知。

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>使应用程序能够接收无提示的推送通知
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>将 Reach 库添加到你的项目
1. 右键单击你的项目。
2. 选择“ **将文件添加到**”。
3. 导航到在其中解压缩 SDK 的文件夹。
4. 选择 `EngagementReach` 文件夹。
5. 单击 **“添加”**。

### <a name="modify-your-application-delegate"></a>修改你的应用程序代理
1. 回到 **AppDeletegate.m** 文件中，导入 Engagement Reach 模块。

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>
2. 在 `application:didFinishLaunchingWithOptions` 方法中，创建 Reach 模块并将其传递到你现有的 Engagement 初始化行︰

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>启用应用接收 APNS 推送通知
1. 将以下行添加到 `application:didFinishLaunchingWithOptions` 方法中：

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }
2. 如下所示添加 `application:didRegisterForRemoteNotificationsWithDeviceToken` 方法：

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
             [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }
3. 如下所示添加 `didFailToRegisterForRemoteNotificationsWithError` 方法：

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           NSLog(@"Failed to get token, error: %@", error);
        }
4. 如下所示添加 `didReceiveRemoteNotification:fetchCompletionHandler` 方法：

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

