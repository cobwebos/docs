---
title: 使用 Azure 通知中心向 Xamarin.Android 应用推送通知 | Microsoft Docs
description: 在本教程中，将了解如何使用 Azure 通知中心将推送通知发送到 Xamarin.Android 应用程序。
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776706"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>教程：使用 Azure 通知中心向 Xamarin.Android 应用推送通知
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概述
本教程说明如何使用 Azure 通知中心将推送通知发送到 Xamarin.Android 应用程序。 创建一个空白 Xamarin.Android 应用，以便使用 Firebase Cloud Messaging (FCM) 接收推送通知。 使用通知中心将推送通知广播到运行应用的所有设备。 [NotificationHubs 应用][GitHub]示例中提供了完成的代码。

在本教程中，请执行以下步骤：

> [!div class="checklist"]
> * 创建 Firebase 项目并启用 Firebase Cloud Messaging
> * 创建通知中心
> * 创建 Xamarin.Android 应用并将其连接到通知中心
> * 从 Azure 门户发送测试通知

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 基于 Windows 的 [Visual Studio with Xamarin] 或基于 OS X 的 [Visual Studio for Mac]。
- 有效的 Google 帐户

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>创建 Firebase 项目并启用 Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>创建通知中心
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>配置通知中心的 GCM 设置

1. 在“通知设置”部分选择“Google (GCM)”。 
2. 输入先前从 Google Firebase Console 记下的**旧服务器密钥**。 
3. 在工具栏上选择“保存”。 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

通知中心已配置为使用 FCM，并且你有连接字符串，既可用于注册应用以接收通知，又可用于发送推送通知。

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>创建 Xamarin.Android 应用并将其连接到通知中心

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>创建 Visual Studio 项目并添加 NuGet 包
1. 在 Visual Studio 中指向“文件”，选择“新建”，然后选择“项目”。 
   
      ![Visual Studio - 新建 Android 项目](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. 在“解决方案资源管理器”窗口中展开“属性”，然后单击“AndroidManifest.xml”。 更新包名，使之与你在 Google Firebase Console 中将 Firebase Cloud Messaging 添加到项目时输入的包名匹配。 

      ![GCM 中的包名](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. 右键单击项目，然后选择“管理 NuGet 包...”。 
4. 选择“浏览”按钮。搜索 **Xamarin.GooglePlayServices.Base**。 在结果列表中选择 **Xamarin.GooglePlayServices.Base**。 然后，选择“安装”。 

      ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. 在“NuGet 包管理器”窗口中，搜索“Xamarin.Firebase.Messaging”。 在结果列表中选择 **Xamarin.Firebase.Messaging**。 然后，选择“安装”。 
6. 现在，请搜索 **Xamarin.Azure.NotificationHubs.Android**。 在结果列表中选择 **Xamarin.Azure.NotificationHubs.Android**。 然后，选择“安装”。 

### <a name="add-the-google-services-json-file"></a>添加 Google Services JSON 文件

1. 将从 Google Firebase Console 下载的 **google-services.json** 复制到项目文件夹。
2. 将 **google-services.json** 添加到项目。
3. 在“解决方案资源管理器”窗口中选择“google-services.json”。
4. 在“属性”窗格中，将“生成操作”设置为“GoogleServicesJson”。 如果看不到 **GoogleServicesJson**，请在关闭 Visual Studio 后将其重新启动，重新打开项目，然后重试。 

      ![GoogleServicesJson 生成操作](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>在项目中设置通知中心

#### <a name="registering-with-firebase-cloud-messaging"></a>注册到 Firebase Cloud Messaging

打开 **AndroidManifest.xml** 文件，并将以下 `<receiver>` 元素插入 `<application>` 元素：

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. 收集有关 Android 应用和通知中心的以下信息：
   
   * **侦听连接字符串**：在 [Azure 门户]中的仪表板上，选择“查看连接字符串”。 复制此值的 *DefaultListenSharedAccessSignature* 连接字符串。
   * **中心名称**：中心在 [Azure 门户]中的名称。 例如 *mynotificationhub2*。
     
2. 在“解决方案资源管理器”窗口中右键单击你的**项目**，指向“添加”，然后选择“类”。 
4. 为 Xamarin 项目创建 **Constants.cs** 类，并在该类中定义以下常量值。 将占位符替换成自己的值。
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. 将以下 using 语句添加到 **MainActivity.cs**：
   
    ```csharp
        using Android.Util;
    ```
4. 将一个实例变量添加到 **MainActivity.cs**，后者用于在运行应用时显示警报对话框：
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. 将以下代码添加到 **MainActivity.cs** 中的 `OnCreate`，位于 `base.OnCreate(savedInstanceState)` 之后：

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```
7. 创建新类 **MyFirebaseIIDService**（类似于所创建的 **Constants** 类）。 
8. 将以下 using 语句添加到 **MyFirebaseIIDService.cs**：
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. 在 **MyFirebaseIIDService.cs** 中添加下面的 **class** 声明，让类从 **FirebaseInstanceIdService** 继承：
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. 在 **MyFirebaseIIDService.cs** 中添加以下代码：
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
11. 为项目创建另一新类，将其命名为 **MyFirebaseMessagingService**。
12. 将以下 using 语句添加到 **MyFirebaseMessagingService.cs**。
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. 将以下内容添加到类声明上方，让类从 **FirebaseMessagingService** 继承：
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. 将以下代码添加到 **MyFirebaseMessagingService.cs**：
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **生成**项目。 
16. 在设备或加载的模拟器上**运行**应用

## <a name="send-test-notification-from-the-azure-portal"></a>从 Azure 门户发送测试通知
可以在 [Azure 门户]中使用“测试性发送”选项，在应用中测试通知的发送。 它会向设备发送测试性的推送通知。

![Azure 门户 - 测试性发送](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

通常，推送通知是在移动服务或 ASP.NET 等后端服务中通过兼容库发送的。 如果后端没有可用库，也可使用 REST API 直接发送通知消息。

## <a name="next-steps"></a>后续步骤
本教程介绍了如何将广播通知发送到所有注册到后端的 Android 设备。 若要了解如何向特定的 Android 设备推送通知，请转到以下教程： 

> [!div class="nextstepaction"]
>[向特定设备推送通知](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio with Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure 门户]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
