---
title: "适用于 Xamarin.iOS 应用的 Azure 通知中心入门 | Microsoft Docs"
description: "在本教程中，将了解如何使用 Azure 通知中心将推送通知发送到 Xamarin iOS 应用程序。"
services: notification-hubs
keywords: "ios 推送通知, 推送消息, 推送通知, 推送消息"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: edb48cd8de9b1f7357c40cea73fa4b9a422cb8fa
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>适用于 Xamarin.iOS 应用的 Azure 通知中心入门
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概述
> [!NOTE]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)。
> 
> 

本教程演示如何使用 Azure 通知中心将推送通知发送到 iOS 应用程序。 将创建一个空白 Xamarin.iOS 应用，它使用 [Apple Push Notification 服务 (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)接收推送通知。 

完成后，能够使用通知中心将推送通知广播到运行应用的所有设备。 [NotificationHubs 应用][GitHub]示例中提供了完成的代码。

本教程演示使用通知中心的简单推送消息广播方案。

## <a name="prerequisites"></a>先决条件
本教程需要的内容如下：

* 最新版本的 [XCode][Install Xcode]
* iOS 10（或更高版本）兼容设备
* [Apple 开发人员计划](https://developer.apple.com/programs/) 成员身份。
* [Visual Studio for Mac]
  
  > [!NOTE]
  > 由于 iOS 推送通知配置要求，必须在物理 iOS 设备（iPhone 或 iPad）而不是在模拟器上部署和测试示例应用程序。
  > 
  > 

只有在完成本教程后，才能完成有关 Xamarin iOS 应用的所有其他通知中心教程。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>针对 iOS 推送通知配置通知中心
本部分介绍如何执行相关步骤，以便使用以前创建的 **.p12** 推送证书创建新的通知中心并配置 APNS 身份验证。 如果想要使用已创建的通知中心，可以跳到步骤 5。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>单击“Notification Services”按钮，然后选择“Apple (APNS)”。<b></b><b></b> 确保选择“证书”，单击文件图标，然后选择此前导出的<b></b> <b>.p12</b> 文件。 确保同时指定正确的密码。</p>

<p>由于此项目用于开发，因此请务必选择“沙盒”模式。<b></b> 仅当想要将推送通知发送给从应用商店购买应用的用户时，才使用“生产”模式。<b></b></p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![在 Azure 门户中配置 APNS][6]

&emsp;&emsp;&emsp;&emsp;![在 Azure 门户中配置 APNS 证书][7]

通知中心现在已配置为使用 APNS，并且你有连接字符串用于注册应用和发送推送通知。

## <a name="connect-your-app-to-the-notification-hub"></a>将应用连接到通知中心
#### <a name="create-a-new-project"></a>创建新项目
1. 在 Visual Studio 中创建新的 iOS 项目并选择“单视图应用”模板，然后单击“下一步”
   
     ![Visual Studio - 选择应用程序类型][31]

2. 输入应用名称和组织标识符，然后点击“下一步”和“创建”。

3. 在“解决方案”视图中双击“Into.plist”，确保“标识”下的捆绑标识符与创建预配配置文件时使用的相符。 在“签名”下，确保选中开发人员帐户；在“团队”下，确保选中“自动管理签名”。这样就会自动选中签名证书和预配配置文件。

    ![Visual Studio- iOS 应用配置][32]

4. 添加 Azure 消息传送包。 在“解决方案”视图中右键单击项目，然后选择“添加” > “添加 NuGet 包”。 搜索 **Xamarin.Azure.NotificationHubs.iOS**，向项目添加该包。

5. 向类添加新文件，将其命名为 **Constants.cs**，然后添加以下变量，将字符串文本占位符替换为此前记下的中心名称和 *DefaultListenSharedAccessSignature*。
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. 在 **AppDelegate.cs**中，添加以下 using 语句：
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. 声明 **SBNotificationHub**的实例：
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. 在 **AppDelegate.cs** 中，更新 **FinishedLaunching()** 以匹配以下内容：
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. 重写 **AppDelegate.cs** 中的 **RegisteredForRemoteNotifications()** 方法：
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. 重写 **AppDelegate.cs** 中的 **ReceivedRemoteNotification()** 方法：
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. 在 **AppDelegate.cs** 中创建以下 **ProcessNotification()** 方法：
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > 可以选择覆盖 **FailedToRegisterForRemoteNotifications()** 以处理无网络连接等情况。 如果用户可能会在脱机模式（例如飞行模式）下启动应用程序，并且你想要处理应用特定的推送消息传送方案，则此操作特别重要。
  

12. 在设备上运行应用程序。

## <a name="sending-test-push-notifications"></a>发送测试性推送通知
可以在 [Azure 门户]中使用“测试性发送”选项，在应用中测试通知的发送。 这样会向设备发送测试性推送通知。

![Azure 门户 - 测试性发送][30]

通常，推送通知是在后端服务（例如，移动应用，或者使用兼容库的 ASP.NET）中发送的。 如果后端没有可用库，也可使用 REST API 直接发送通知消息。

建议下一步查看[使用通知中心向用户推送通知](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教程，了解如何从 ASP.NET 后端发送通知。 不过，可以使用以下方法来发送通知：

下面是可能需要查看的有关发送通知的其他教程列表：
* REST 接口：可以使用 [REST 接口](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)在任何后端平台上支持推送通知。
* **Microsoft Azure 通知中心 .NET SDK**：在 Visual Studio 的 Nuget 包管理器中，运行 [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。
* Node.js：[如何通过 Node.js 使用通知中心](notification-hubs-nodejs-push-notification-tutorial.md)。
* Java / PHP**：有关如何使用 REST API 发送推送通知的示例，请参阅“如何通过 Java/PHP 使用通知中心”([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md))。

## <a name="next-steps"></a>后续步骤
在这个简单的示例中，已将推送通知广播到所有 iOS 设备。 要针对特定客户，请参考教程 [使用通知中心将通知推送到用户]。 如果要按兴趣组划分用户，可以阅读 [使用通知中心发送突发新闻]。 请在[通知中心指南]和[适用于 iOS 的通知中心操作方法指南]中了解有关如何使用通知中心的详细信息。

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[通知中心指南]: http://msdn.microsoft.com/library/jj927170.aspx
[适用于 iOS 的通知中心操作方法指南]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[使用通知中心将通知推送到用户]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中心发送突发新闻]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[在 Azure 应用服务中创建 Java Web 应用]: https://portal.azure.com
