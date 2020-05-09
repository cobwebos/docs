---
title: 使用 Azure 通知中心将推送通知发送到 Xamarin | Microsoft Docs
description: 本教程介绍如何使用 Azure 通知中心将推送通知发送到 Xamarin.iOS 应用程序。
services: notification-hubs
keywords: ios 推送通知, 推送消息, 推送通知, 推送消息
documentationcenter: xamarin
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 07417427385806e61db0d7d83624d923e92eb693
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80127014"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>教程：使用 Azure 通知中心向 Xamarin.iOS 应用发送推送通知

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概述

本教程演示如何使用 Azure 通知中心将推送通知发送到 iOS 应用程序。 你将创建一个空白 Xamarin.iOS 应用，它使用 [Apple Push Notification 服务 (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) 接收推送通知。

完成后，你将能够使用通知中心将推送通知广播到运行你的应用的所有设备。 [NotificationHubs 应用][GitHub]示例中提供了完成的代码。

在本教程中，你创建/更新代码来执行以下任务：

> [!div class="checklist"]
> * 生成证书签名请求文件
> * 为推送通知注册应用程序
> * 为应用程序创建配置文件
> * 针对 iOS 推送通知配置通知中心
> * 发送测试推送通知

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前[创建一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 最新版本的 [Xcode][Install Xcode]
* iOS 10（或更高版本）兼容设备
* [Apple 开发人员计划](https://developer.apple.com/programs/) 成员身份。
* [Visual Studio for Mac]
  
  > [!NOTE]
  > 由于 iOS 推送通知配置要求，必须在物理 iOS 设备（iPhone 或 iPad）而不是在模拟器上部署和测试示例应用程序。

只有在完成本教程后，才能完成有关 Xamarin iOS 应用的所有其他通知中心教程。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>将应用连接到通知中心

### <a name="create-a-new-project"></a>创建新项目

1. 在 Visual Studio 中创建新的 iOS 项目并选择“单视图应用”模板，然后单击“下一步”  

     ![Visual Studio - 选择应用程序类型][31]

2. 输入应用名称和组织标识符，然后单击“下一步”和“创建”。  

3. 在“解决方案”视图中双击“Info.plist”，  确保“标识”下  的捆绑标识符与创建预配配置文件时使用的相符。 在“签名”下，确保  选中开发人员帐户；在“团队”下，确保选中“自动管理签名”。这样就会自动选中签名证书和预配配置文件。 

    ![Visual Studio- iOS 应用配置][32]

4. 在“解决方案”视图中双击 `Entitlements.plist`，确保选中“启用推送通知”。 

    ![Visual Studio - iOS 授权配置][33]

5. 添加 Azure 消息传送包。 在“解决方案”视图中右键单击项目，然后选择“添加”   >   “添加 NuGet 包”。 搜索 **Xamarin.Azure.NotificationHubs.iOS**，向项目添加该包。

6. 向类添加新文件，将其命名为 `Constants.cs`，然后添加以下变量，将字符串文本占位符替换为此前记下的 `hubname` 和 `DefaultListenSharedAccessSignature`。

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. 在 `AppDelegate.cs` 中，添加以下 using 语句：

    ```csharp
    using WindowsAzure.Messaging;
    using UserNotifications
    ```

8. 声明 `SBNotificationHub` 的实例：

    ```csharp
    private SBNotificationHub Hub { get; set; }
    ```

9. 在 `AppDelegate.cs` 中更新 `FinishedLaunching()`，使之与以下代码匹配：

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Badge | UNAuthorizationOptions.Sound,
                                                                    (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
        } else if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
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

10. 在 `AppDelegate.cs` 中重写 `RegisteredForRemoteNotifications()` 方法：

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);

        Hub.UnregisterAll (deviceToken, (error) => {
            if (error != null)
            {
                System.Diagnostics.Debug.WriteLine("Error calling Unregister: {0}", error.ToString());
                return;
            }

            NSSet tags = null; // create tags if you want
            Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                if (errorCallback != null)
                    System.Diagnostics.Debug.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
            });
        });
    }
    ```

11. 在 `AppDelegate.cs` 中重写 `ReceivedRemoteNotification()` 方法：

    ```csharp
    public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
    {
        ProcessNotification(userInfo, false);
    }
    ```

12. 在 `AppDelegate.cs` 中创建 `ProcessNotification()` 方法：

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
    > 可以选择重写 `FailedToRegisterForRemoteNotifications()` 以处理无网络连接等情况。 如果用户可能会在脱机模式（例如飞行模式）下启动应用程序，并且你想要处理应用特定的推送消息传送方案，则此操作特别重要。

13. 在设备上运行应用程序。

## <a name="send-test-push-notifications"></a>发送测试推送通知

 可以在 [Azure 门户]中使用“测试性发送”选项，在应用中测试通知的发送。 它会向设备发送测试性的推送通知。

![Azure 门户 - 测试性发送][30]

通常，推送通知是在后端服务（例如，移动应用，或者使用兼容库的 ASP.NET）中发送的。 如果后端没有可用库，也可使用 REST API 直接发送通知消息。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将广播通知发送到所有注册到后端的 iOS 设备。 若要了解如何向特定的 iOS 设备推送通知，请转到以下教程：

> [!div class="nextstepaction"]
>[向特定设备推送通知](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure 门户]: https://portal.azure.com
