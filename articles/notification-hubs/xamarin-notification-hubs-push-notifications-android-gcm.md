---
title: 通知中心入门 - Xamarin.Android 应用 | Microsoft Docs
description: 在本教程中，将了解如何使用 Azure 通知中心将推送通知发送到 Xamarin.Android 应用程序。
author: jwhitedev
manager: kpiteira
editor: ''
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7fee7813bbdcf902d5f5ae2d0af7540c8899ad25
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>适用于 Xamarin.Android 应用的通知中心入门
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概述
本教程说明如何使用 Azure 通知中心将推送通知发送到 Xamarin.Android 应用程序。 创建一个空白 Xamarin.Android 应用，以便使用 Firebase Cloud Messaging (FCM) 接收推送通知。 完成后，能够使用通知中心将推送通知广播到运行应用的所有设备。 [NotificationHubs 应用][GitHub]示例中提供了完成的代码。

本教程演示使用通知中心的简单广播方案。

## <a name="before-you-begin"></a>开始之前
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

可以在 GitHub 上的 [此处][GitHub]找到本教程的已完成代码。

## <a name="prerequisites"></a>先决条件
本教程需要的内容如下：

* 基于 Windows 的 [Visual Studio with Xamarin] 或基于 OS X 的 [Visual Studio for Mac]。
* 有效的 Google 帐户

完成本教程是学习有关 Xamarin.Android 应用的所有其他通知中心教程的先决条件。

> [!IMPORTANT]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)。
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>启用 Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>配置通知中心
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>选择顶部的“配置”<b></b>选项卡，输入在上一部分获得的“API 密钥”<b></b>值，然后选择“保存”<b></b>。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

通知中心已配置为使用 FCM，并且你有连接字符串，既可用于注册应用以接收通知，又可用于发送推送通知。

## <a name="connect-your-app-to-the-notification-hub"></a>将应用连接到通知中心
首先，创建新项目。 

1. 在 Visual Studio 中选择“新建解决方案” > “Android 应用”，然后选择“下一步”。
   
      ![Visual Studio - 新建 Android 项目][22]

2. 输入“应用名称”和“标识符”。 选择要支持的“目标平台”，然后选择“下一步”和“创建”。
   
      ![Visual Studio - Android 应用配置][23]

    这会创建一个新的 Android 项目。

3. 通过在“解决方案”视图中右键单击新项目，然后选择“选项”，打开项目属性。 选择“生成”部分中的“Android 应用程序”项。
   
    确保“包名称”的第一个字母为小写形式。
   
   > [!IMPORTANT]
   > 包名称的第一个字母必须为小写形式。 否则，在为以下推送通知注册应用时，会收到应用程序清单错误。
   > 
   > 
   
      ![Visual Studio - Android 项目选项][24]
4. （可选）将“最低 Android 版本”设置为另一个 API 级别。
5. （可选）将“目标 Android 版本”设置为要针对的另一个 API 版本（必须是 API 级别 8 或更高）。
6. 选择“确定”并关闭“项目选项”对话框。

### <a name="add-the-required-packages-to-your-project"></a>将所需包添加到项目

1. 右键单击项目，然后选择“添加” > “添加 NuGet 包”
2. 搜索 **Xamarin.Azure.NotificationHubs.Android**，将其添加到项目。
3. 搜索 **Xamarin.Firebase.Messaging**，将其添加到项目。

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
   * **中心名称**：这是中心在 [Azure 门户]上的名称。 例如 *mynotificationhub2*。
     
2. 为 Xamarin 项目创建 **Constants.cs** 类，并在该类中定义以下常量值。 将占位符替换成自己的值。
    
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

6. 右键单击项目，添加此前从 Firebase 项目下载的 `google-services.json`。 右键单击添加的文件，将生成操作设置为 `GoogleServicesJson`

    ![Visual Studio - 配置 google-services.json][25]

7. 创建新类 **MyFirebaseIIDService**。

8. 将以下 using 语句添加到 **MyFirebaseIIDService.cs**：
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. 在 **MyFirebaseIIDService.cs** 中将以下内容添加到 **class** 声明上方，让类从 **FirebaseInstanceIdService** 继承：
   
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
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
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

15. 在设备或加载的模拟器上运行应用

## <a name="send-notifications-from-the-portal"></a>从门户发送通知
可以在 [Azure 门户]中使用“测试性发送”选项，在应用中测试通知的发送。 这样会向设备发送测试性推送通知。

![Azure 门户 - 测试性发送][30]

通常，推送通知是在移动服务或 ASP.NET 等后端服务中通过兼容库发送的。 如果后端没有可用库，也可使用 REST API 直接发送通知消息。

下面是可能需要查看的有关发送通知的其他教程列表：

* ASP.NET：请参阅 [使用通知中心向用户推送通知]。
* Azure 通知中心 Java SDK：有关从 Java 发送通知的信息，请参阅 [如何通过 Java 使用通知中心](notification-hubs-java-push-notification-tutorial.md) 。 这种方法已在 Eclipse for Android 开发环境中进行测试。
* PHP：请参阅 [如何通过 PHP 使用通知中心](notification-hubs-php-push-notification-tutorial.md)。

## <a name="next-steps"></a>后续步骤
在这个简单的示例中，将通知广播到所有 Android 设备。 要针对特定客户，请参考教程 [使用通知中心向用户推送通知]。 如果要按兴趣组划分用户，可以阅读 [使用通知中心发送突发新闻]。 在[通知中心指南]和[针对 Android 的通知中心操作指南]中了解有关如何使用通知中心的详细信息。

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
[通知中心指南]: http://msdn.microsoft.com/library/jj927170.aspx
[针对 Android 的通知中心操作指南]: http://msdn.microsoft.com/library/dn282661.aspx

[使用通知中心向用户推送通知]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中心发送突发新闻]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
