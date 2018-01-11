---
title: "适用于 iOS 应用的 Azure 通知中心入门 | Microsoft Docs"
description: "在本教程中，将了解如何使用 Azure 通知中心将推送通知发送到 iOS 应用程序。"
services: notification-hubs
documentationcenter: ios
keywords: "推送通知, 推送通知, ios 推送通知"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0e9e7ab196eef790b74074be319cd8122cf3ff5c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-ios-apps"></a>适用于 iOS 应用的 Azure 通知中心入门
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概述
> [!NOTE]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)。
> 
> 

本教程演示如何使用 Azure 通知中心将推送通知发送到 iOS 应用程序。 将创建一个空白 iOS 应用，它使用 [Apple Push Notification 服务 (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)接收推送通知。 

完成后，即可使用通知中心将推送通知广播到运行应用的所有设备。

## <a name="before-you-begin"></a>开始之前
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

可以 [在 GitHub 上](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)找到本教程的已完成代码。 

## <a name="prerequisites"></a>先决条件
本教程需要的内容如下：

* [Windows Azure Messaging Framework]
* 最新版本的 [Xcode]
* 支持 iOS 10（或更高版本）的设备
* [Apple 开发人员计划](https://developer.apple.com/programs/) 成员身份。
  
  > [!NOTE]
  > 由于推送通知的配置要求，必须在物理 iOS 的设备（iPhone 或 iPad）而不是在 iOS 模拟器上部署和测试推送通知。
  > 
  > 

完成本教程是学习有关 iOS 应用的所有其他通知中心教程的先决条件。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>针对 iOS 推送通知配置通知中心
本部分介绍如何执行相关步骤，以便使用以前创建的 **.p12** 推送证书创建新的通知中心并配置 APNS 身份验证。 如果想要使用已创建的通知中心，可以跳到步骤 5。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>在“Notification Services”下选择“Apple (APNS)”。<b></b><b></b> 确保选择“证书”，单击文件图标，然后选择此前导出的<b></b> <b>.p12</b> 文件。 确保同时指定正确的密码。</p>

<p>由于此项目用于开发，因此请务必选择“沙盒”模式。<b></b> 仅当想要将推送通知发送给从应用商店购买应用的用户时，才使用“生产”模式。<b></b></p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![在 Azure 门户中配置 APNS][6]

&emsp;&emsp;&emsp;&emsp;![在 Azure 门户中配置 APNS 证书][7]

现在已为通知中心配置 APNS，并且有了用于注册应用和发送推送通知的连接字符串。

## <a name="connect-your-ios-app-to-notification-hubs"></a>将 iOS 应用连接到通知中心
1. 在 Xcode 中，创建新的 iOS 项目，并选择“单视图应用程序”模板。
   
    ![Xcode — 单一视图应用程序][8]
    
2. 设置新项目的选项时，请务必使用在 Apple 开发人员门户中设置捆绑标识符时使用的同一**产品名称**和**组织标识符**。
   
    ![Xcode — 项目选项][11]
    
3. 在“项目导航器”下单击项目名称，然后单击“常规”选项卡，找到“签名”。 确保为 Apple 开发人员帐户选择适当的团队。 XCode 会根据捆绑标识符自动下拉以前创建的预配配置文件。
   
    如果屏幕未显示在 Xcode 中创建的新预配配置文件，请尝试刷新签名标识的配置文件。 单击菜单栏上的“Xcode”，再依次单击“首选项”、“帐户”选项卡、“查看详细信息”按钮、签名标识，然后单击右下角的刷新按钮。

    ![Xcode — 预配配置文件][9]

4. 选择“功能”选项卡，确保启用“推送通知”

    ![Xcode - 推送功能][12]
   
5. 下载 [Windows Azure Messaging Framework]，然后解压缩该文件。 在 Xcode 中，右键单击项目，然后单击“将文件添加到”选项，将 **WindowsAzureMessaging.framework** 文件夹添加到 Xcode 项目。 选择“选项”，确保选中“根据需要复制项目”，然后单击“添加”。

    ![解压缩 Azure SDK][10]

6. 将新的标头文件添加到名为 **HubInfo.h** 的项目。 此文件将保存通知中心的常量。 添加以下定义，然后将字符串文本占位符替换为*中心名称*以及前面记下的 *DefaultListenSharedAccessSignature*。

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. 打开 **AppDelegate.h** 文件并添加以下导入指令：

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. 在 **AppDelegate.m** 文件的 **didFinishLaunchingWithOptions** 方法中添加以下代码，具体取决于 iOS 版本。 此代码将向 APNs 注册设备句柄：

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. 在同一文件中添加以下方法：

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    此代码将使用在 HubInfo.h 中指定的连接信息连接到通知中心。 然后，它向通知中心提供设备令牌，使通知中心能够发送通知。

10. 在同一文件中，添加以下方法以便在应用处于活动状态时收到通知的情况下显示 **UIAlert** ：

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. 若要验证是否没有故障，请在设备上生成并运行应用。

## <a name="send-test-push-notifications"></a>发送测试推送通知
可以在 [Azure 门户]中使用“测试性发送”选项，在应用中测试通知的发送。 这样会向设备发送测试性推送通知。

![Azure 门户 - 测试性发送][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="checking-if-your-app-can-receive-push-notifications"></a>检查应用能否接收推送通知
要在 iOS 上测试推送通知，必须将应用部署到物理 iOS 设备。 不能使用 iOS 模拟器发送 Apple 推送通知。

1. 运行应用并验证注册是否成功，并按“确定”。
   
    ![iOS 应用推送通知注册测试][33]
2. 如上所述，接下来可以从 [Azure 门户]发送测试性推送通知。 

3. 该推送通知会从特定通知中心发送到所有已注册为接收通知的设备。
   
    ![iOS 应用推送通知接收测试][35]

## <a name="next-steps"></a>后续步骤
在这个简单的示例中，已将推送通知广播到所有已注册的 iOS 设备。 在学习时，建议下一步开始 [Azure 通知中心 - 使用 .NET 后端通知 iOS 用户]教程。 本指南将介绍如何创建一个后端，以便使用标记发送推送通知。 

如果要按兴趣组划分用户，可以进一步阅读 [使用通知中心发送突发新闻] 教程。 

有关通知中心的常规信息，请参阅 [通知中心指南]。

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[通知中心指南]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure 通知中心 - 使用 .NET 后端通知 iOS 用户]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[使用通知中心发送突发新闻]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure 门户]: https://portal.azure.com
