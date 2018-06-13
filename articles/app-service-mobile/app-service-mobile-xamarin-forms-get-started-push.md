---
title: 向 Xamarin.Forms 应用添加推送通知 | Microsoft Docs
description: 了解如何使用 Azure 服务将多平台推送通知发送到 Xamarin.Forms 应用。
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 0bea00d411205541684e807182abd3236c09bd9d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
ms.locfileid: "30164424"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>向 Xamarin.Forms 应用添加推送通知
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概述
在本教程中，可以将推送通知添加到从 [Xamarin.Forms 快速启动](app-service-mobile-xamarin-forms-get-started.md)生成的所有项目。 这意味着，每次插入一条记录，都会向所有跨平台客户端发送一条推送通知。

如果不使用下载的快速入门服务器项目，则需要推送通知扩展包。 有关详细信息，请参阅[使用用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="prerequisites"></a>先决条件
对于 iOS，用户需要 [Apple 开发人员计划成员身份](https://developer.apple.com/programs/ios/)和物理 iOS 设备。 [iOS 模拟器不支持推送通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。

## <a name="configure-hub"></a>配置通知中心
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>更新服务器项目以发送推送通知
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>配置和运行 Android 项目（可选）
完成此部分可为 Android 的 Xamarin.Forms Droid 项目启用推送通知。

### <a name="enable-firebase-cloud-messaging-fcm"></a>启用 Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>使用 FCM 配置移动应用后端以发送推送请求
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>向 Android 项目添加推送通知
使用 FCM 配置后端以后，即可将组件和代码添加到客户端，以便通过 FCM 注册。 也可通过移动应用后端向 Azure 通知中心注册推送通知，然后即可接收通知。

1. 在 **Droid** 项目中，右键单击“引用”>“管理 NuGet 包...”。
1. 在“NuGet 包管理器”窗口中，搜索“Xamarin.Firebase.Messaging”包并将其添加到项目中。
1. 在 **Droid** 项目的项目属性中，将应用设置为使用 Android 7.0 版或更高版本进行编译。
1. 将从 Firebase 控制台下载的 **google-services.json** 文件添加到 **Droid** 项目的根目录，并将其生成操作设置为 **GoogleServicesJson**。 有关详细信息，请参阅[添加 Google 服务 JSON 文件](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File)。

#### <a name="registering-with-firebase-cloud-messaging"></a>注册到 Firebase Cloud Messaging

1. 打开 **AndroidManifest.xml** 文件，并将以下 `<receiver>` 元素插入 `<application>` 元素：

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>实现 Firebase 实例 ID 服务

1. 将新类添加到名为 `FirebaseRegistrationService` 的 **Droid** 项目，并确保文件顶部存在以下 `using` 语句：

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. 将空的 `FirebaseRegistrationService` 类替换为以下代码：

        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class FirebaseRegistrationService : FirebaseInstanceIdService
        {
            const string TAG = "FirebaseRegistrationService";

            public override void OnTokenRefresh()
            {
                var refreshedToken = FirebaseInstanceId.Instance.Token;
                Log.Debug(TAG, "Refreshed token: " + refreshedToken);
                SendRegistrationTokenToAzureNotificationHub(refreshedToken);
            }

            void SendRegistrationTokenToAzureNotificationHub(string token)
            {
                // Update notification hub registration
                Task.Run(async () =>
                {
                    await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
                });
            }
        }

    `FirebaseRegistrationService` 类负责生成用于授权应用程序访问 FCM 的安全令牌。 当应用程序收到来自 FCM 的注册令牌时，会调用 `OnTokenRefresh` 方法。 此方法从 `FirebaseInstanceId.Instance.Token` 属性检索令牌，该属性通过 FCM 以异步方式更新。 很少调用 `OnTokenRefresh` 方法，因为仅当以下情况下才会更新令牌：安装或卸载应用程序、用户删除应用程序数据、应用程序清除实例 ID、令牌的安全性已受到威胁。 此外，FCM 实例 ID 服务将请求应用程序定期刷新其令牌，通常为每 6 个月一次。

    `OnTokenRefresh` 方法还会调用 `SendRegistrationTokenToAzureNotificationHub` 方法，该方法用于将用户的注册令牌与 Azure 通知中心相关联。

#### <a name="registering-with-the-azure-notification-hub"></a>注册到 Azure 通知中心

1. 将新类添加到名为 `AzureNotificationHubService` 的 **Droid** 项目，并确保文件顶部存在以下 `using` 语句：

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. 将空的 `AzureNotificationHubService` 类替换为以下代码：

        public class AzureNotificationHubService
        {
            const string TAG = "AzureNotificationHubService";

            public static async Task RegisterAsync(Push push, string token)
            {
                try
                {
                    const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                    JObject templates = new JObject();
                    templates["genericMessage"] = new JObject
                    {
                        {"body", templateBody}
                    };

                    await push.RegisterAsync(token, templates);
                    Log.Info("Push Installation Id: ", push.InstallationId.ToString());
                }
                catch (Exception ex)
                {
                    Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
                }
            }
        }

    `RegisterAsync` 方法创建 JSON 格式的简单通知消息模板，并使用 Firebase 注册令牌注册以接收来自通知中心的模板通知。 这可确保从 Azure 通知中心发送的任何通知都将发送到注册令牌所表示的设备。

#### <a name="displaying-the-contents-of-a-push-notification"></a>显示推送通知的内容

1. 将新类添加到名为 `FirebaseNotificationService` 的 **Droid** 项目，并确保文件顶部存在以下 `using` 语句：

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V7.App;
        using Android.Util;
        using Firebase.Messaging;

1. 将空的 `FirebaseNotificationService` 类替换为以下代码：

        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class FirebaseNotificationService : FirebaseMessagingService
        {
            const string TAG = "FirebaseNotificationService";

            public override void OnMessageReceived(RemoteMessage message)
            {
                Log.Debug(TAG, "From: " + message.From);

                // Pull message body out of the template
                var messageBody = message.Data["message"];
                if (string.IsNullOrWhiteSpace(messageBody))
                    return;

                Log.Debug(TAG, "Notification message body: " + messageBody);
                SendNotification(messageBody);
            }

            void SendNotification(string messageBody)
            {
                var intent = new Intent(this, typeof(MainActivity));
                intent.AddFlags(ActivityFlags.ClearTop);
                var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

                var notificationBuilder = new NotificationCompat.Builder(this)
                    .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                    .SetContentTitle("New Todo Item")
                    .SetContentText(messageBody)
                    .SetContentIntent(pendingIntent)
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                    .SetAutoCancel(true);

                var notificationManager = NotificationManager.FromContext(this);
                notificationManager.Notify(0, notificationBuilder.Build());
            }
        }

    `OnMessageReceived` 方法（应用程序接收来自 FCM 的通知时调用）提取消息内容并调用 `SendNotification` 方法。 此方法将消息内容转换为运行应用程序时启动的本地通知，并将通知显示在通知区域中。

现在，已准备好在 Android 设备或模拟器上运行的应用中测试推送通知。

### <a name="test-push-notifications-in-your-android-app"></a>在 Android 应用中测试推送通知
只有在模拟器上测试时才需要前两个步骤。

1. 请确保部署到配置了 Google Play Services 的设备或模拟器，或者在该设备或模拟器上进行调试。 这可以通过检查设备或模拟器上是否安装了“播放”应用来进行验证。
2. 单击“应用” > “设置” > “添加帐户”，将 Google 帐户添加到 Android 设备。 然后按提示操作，将现有的 Google 帐户添加到设备，或者新建一个。
3. 在 Visual Studio 或 Xamarin Studio 中，右键单击 **Droid** 项目，并单击“设为启动项目”。
4. 单击“运行”生成项目，并在 Android 设备或模拟器中启动应用。
5. 在应用中，键入一项任务，并单击加号 (**+**) 图标。
6. 确认在添加该项目时收到了通知。

## <a name="configure-and-run-the-ios-project-optional"></a>配置和运行 iOS 项目（可选）
本部分用于运行适用于 iOS 设备的 Xamarin iOS 项目。 如果不使用 iOS 设备，可以跳过本部分。

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>为 APNS 配置通知中心
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

接下来，会在 Xamarin Studio 或 Visual Studio 中配置 iOS 项目设置。

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>向 iOS 应用添加推送通知
1. 在 **iOS** 项目中，打开 AppDelegate.cs，并在代码文件的顶部添加以下语句。

        using Newtonsoft.Json.Linq;
2. 在 **AppDelegate** 类中，为 **RegisteredForRemoteNotifications** 事件添加重写以注册通知：

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. 并在 **AppDelegate** 中，为 **DidReceiveRemoteNotification** 事件处理程序添加以下重写：

        public override void DidReceiveRemoteNotification(UIApplication application,
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    运行该应用时，此方法可处理传入通知。
4. 在 **AppDelegate** 类中，向 **FinishedLaunching** 方法添加以下代码：

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    这会启用对远程通知的支持并请求推送注册。

应用现已更新，可支持推送通知。

#### <a name="test-push-notifications-in-your-ios-app"></a>在 iOS 应用中测试推送通知
1. 右键单击 iOS 项目，并单击“设为启动项目” 。
2. 在 Visual Studio 中按“运行”按钮或 **F5** 生成项目并在 iOS 设备中启动应用， 然后单击“确定”接受推送通知。

   > [!NOTE]
   > 必须显式接受来自应用程序的推送通知。 此请求只会在首次运行应用程序时出现。
   >
   >
3. 在应用中，键入一项任务，并单击加号 (**+**) 图标。
4. 检查是否已收到通知，并单击“确定”以取消通知。

## <a name="configure-and-run-windows-projects-optional"></a>配置和运行 Windows 项目（可选）
本部分用于运行适用于 Windows 设备的 Xamarin.Forms WinApp 和 WinPhone81 项目。 这些步骤也支持通用 Windows 平台 (UWP) 项目。 如果不使用 Windows 设备，可以跳过本部分。

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>将用于推送通知的 Windows 应用注册到 Windows 通知服务 (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>为 WNS 配置通知中心
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>向 Windows 应用添加推送通知
1. 在 Visual Studio 中，打开 Windows 项目中的 **App.xaml.cs**，并添加以下语句。

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    将 `<your_TodoItemManager_portable_class_namespace>` 替换为包含 `TodoItemManager` 类的可移植项目的命名空间。
2. 在 App.xaml.cs 中，添加以下 **InitNotificationsAsync** 方法：

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS =
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    此方法获取推送通知通道，并注册模板以从通知中心接收模板通知。 支持 *messageParam* 的模板通知将传送到此客户端。
3. 在 App.xaml.cs 中，通过添加 `async` 修饰符更新 **OnLaunched** 事件处理程序方法定义。 然后，在方法的末尾添加以下代码行：

        await InitNotificationsAsync();

    这可确保每次启动应用时，创建或刷新推送通知注册。 请务必执行此操作，以保证 WNS 推送通道始终处于活动状态。  
4. 在 Visual Studio 的解决方案资源管理器中，打开 **Package.appxmanifest** 文件，并在“通知”下将“支持 Toast 通知”设置为“是”。
5. 生成该应用并确认没有错误。 客户端应用现在应从移动应用后端注册模板通知。 对于解决方案中的每个 Windows 项目，重复此部分的操作。

#### <a name="test-push-notifications-in-your-windows-app"></a>在 Windows 应用中测试推送通知
1. 在 Visual Studio 中，右键单击 Windows 项目，并单击“设为启动项目”。
2. 按“运行”按钮生成项目并启动应用程序  。
3. 在应用中，为新 todoitem 键入一个名称，并单击加号 (**+**) 图标以添加它。
4. 确认在添加项时收到了通知。

## <a name="next-steps"></a>后续步骤
可以了解有关推送通知的详细信息：

* [从 Azure 移动应用发送推送通知](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [使用 Firebase Cloud Messaging 发送远程通知](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [诊断推送通知问题](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  有多种原因可能导致通知被丢弃或最终未到达设备。 本主题演示如何分析和确定推送通知失败的根本原因。

也可继续学习以下教程之一：

* [向应用添加身份验证](app-service-mobile-xamarin-forms-get-started-users.md)  
  了解如何使用标识提供者对应用的用户进行身份验证。
* [为应用启用脱机同步](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  了解如何使用移动应用后端向应用添加脱机支持。 使用脱机同步，用户可以与移动应用进行交互（查看、添加或修改数据），即使在没有网络连接时也是如此。

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
