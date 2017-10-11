---
title: "向 Xamarin.Forms 应用添加推送通知 | Microsoft Docs"
description: "了解如何使用 Azure 服务将多平台推送通知发送到 Xamarin.Forms 应用。"
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: 912367636f1b26b3b07fbd5fe3fe8ed053218fd5
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
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
使用 FCM 配置后端以后，即可将组件和代码添加到客户端，以便通过 FCM 注册。 还可通过移动应用后端向 Azure 通知中心注册推送通知，并接收通知。

1. 在 **Droid** 项目中，右键单击 **Components** 文件夹，并单击“获取更多组件...”。 然后搜索 **Google Cloud Messaging 客户端**组件并将其添加到项目。 此组件支持 Xamarin Android 项目的推送通知。
2. 打开 MainActivity.cs 项目文件，在文件顶部添加以下语句：

        using Gcm.Client;
3. 调用 **LoadApplication** 后，将以下代码添加到 **OnCreate** 方法：

        try
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }
4. 添加新的 **CreateAndShowDialog** 帮助器方法，如下所示：

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. 将以下代码添加到 **MainActivity** 类：

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    这会公开当前的 **MainActivity** 实例，以便在主 UI 线程上执行。
6. 初始化 **OnCreate** 方法开头的变量 `instance`，如下所示。

        // Set the current instance of MainActivity.
        instance = this;
7. 将新的类文件添加到名为 `GcmService.cs` 的 **Droid** 项目，确保文件的顶部存在以下 **using** 语句：

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;
8. 在文件顶部的 **using** 语句后、**命名空间**声明前，添加以下权限请求。

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. 将以下类定义添加到命名空间。

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > 用之前记下的项目编号替换 **<PROJECT_NUMBER>**。    
   >
   >
10. 用以下代码（代码使用新的广播接收器）替换空的 **GcmService** 类：

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. 将以下代码添加到 **GcmService** 类。 这样会重写 **OnRegistered** 事件处理程序并实现 **Register** 方法。

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

    请注意，此代码使用模板注册中的 `messageParam` 参数。
12. 添加以下代码，实现 **OnMessage**：

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    这会处理传入通知并将其发送到通知管理器进行显示。
13. **GcmServiceBase** 还要求实现 **OnUnRegistered** 和 **OnError** 处理程序方法，操作方法如下：

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

现在，已准备好在 Android 设备或模拟器上运行的应用中测试推送通知。

### <a name="test-push-notifications-in-your-android-app"></a>在 Android 应用中测试推送通知
只有在模拟器上测试时才需要前两个步骤。

1. 请确保在将 Google API 设置为目标的虚拟设备上部署或调试，如下方的 Android 虚拟设备管理器所示。
2. 单击“应用” > “设置” > “添加帐户”，将 Google 帐户添加到 Android 设备。 然后按提示操作，将现有的 Google 帐户添加到设备，或者新建一个。
3. 在 Visual Studio 或 Xamarin Studio 中，右键单击 **Droid** 项目，并单击“设为启动项目”。
4. 单击“运行”生成项目，并在 Android 设备或模拟器中启动应用。
5. 在应用中，键入一项任务，并单击加号 (**+**) 图标。
6. 确认在添加该项目时收到了通知。

## <a name="configure-and-run-the-ios-project-optional"></a>配置和运行 iOS 项目（可选）
本部分用于运行适用于 iOS 设备的 Xamarin iOS 项目。 如果不使用 iOS 设备，可以跳过本部分。

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

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
2. 在 Visual Studio 中按“运行”按钮或 **F5** 生成项目并在 iOS 设备中启动应用， 然后单击**确定**接受推送通知。

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
3. 在 App.xaml.cs 中，通过添加 `async` 修饰符更新 **OnLaunched** 事件处理程序方法定义。 然后在此方法的末尾添加以下代码行：

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

* [诊断推送通知问题](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  有多种原因可能导致通知被丢弃或最终未到达设备。 本主题演示如何分析和确定推送通知失败的根本原因。

也可继续学习以下教程之一：

* [向应用添加身份验证](app-service-mobile-xamarin-forms-get-started-users.md)  
  了解如何使用标识提供者对应用程序的用户进行身份验证。
* [为应用启用脱机同步](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  了解如何使用移动应用后端向应用添加脱机支持。 使用脱机同步，用户可以与移动应用进行交互（查看、添加或修改数据），即使在没有网络连接时也是如此。

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
