---
title: "通知中心入门 - Xamarin.Android 应用 | Microsoft Docs"
description: "在本教程中，你将了解如何使用 Azure 通知中心将推送通知发送到 Xamarin.Android 应用程序。"
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
ms.translationtype: Human Translation
ms.sourcegitcommit: cafe4f2d9ae9a79fd2e27b8734bda43bb774eeb2
ms.openlocfilehash: e0ef1b006a2b202c08a71caaff4ef4d763d50d0a
ms.contentlocale: zh-cn
ms.lasthandoff: 01/05/2017


---
# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Xamarin for Android 通知中心入门
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概述
本教程说明如何使用 Azure 通知中心将推送通知发送到 Xamarin.Android 应用程序。
你将创建一个空白 Xamarin.Android 应用，它使用 Google Cloud Messaging (GCM) 接收推送通知。 完成后，你将能够使用通知中心将推送通知广播到运行你的应用的所有设备。 [NotificationHubs 应用][GitHub]示例中提供了完成的代码。

本教程演示使用通知中心的简单广播方案。

## <a name="before-you-begin"></a>开始之前
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

可以在 GitHub 上的 [此处](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid)找到本教程的已完成代码。

## <a name="prerequisites"></a>先决条件
本教程需要的内容如下：

* Windows 上的 Visual Studio with Xamarin，或者 Mac OS X 上的 Xamarin Studio。 [Visual Studio 和 Xamarin 的设置和安装](https://msdn.microsoft.com/library/mt613162.aspx)中提供了完整的安装说明。
* 有效的 Google 帐户
* [Azure 消息传送组件]
* [Google Cloud Messaging 客户端组件]

完成本教程是学习有关 Xamarin.Android 应用的所有其他通知中心教程的先决条件。

> [!IMPORTANT]
> 若要完成本教程，你必须有一个有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)。
> 
> 

## <a name="enable-google-cloud-messaging"></a>启用 Google Cloud Messaging
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>配置通知中心
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">

<li><p>单击顶部的“配置”<b></b>选项卡，输入在上一部分中获得的“API 密钥”<b></b>值，然后单击“保存”<b></b>。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

你的通知中心现在已配置为使用 GCM，并且你有连接字符串，既可用于注册你的应用以接收通知，又可用于发送推送通知。

## <a name="connect-your-app-to-the-notification-hub"></a>将你的应用连接到通知中心
### <a name="create-a-new-project"></a>创建新项目
1. 在 Xamarin Studio 中，依次单击“新建解决方案”、“Android 应用”、“下一步”。
   
      ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. 输入你的“应用名称”和“标识符”。 单击你要支持的“目标平台”，然后单击“下一步”和“创建”。
   
      ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)

    这将会创建一个新的 Android 项目。

1. 通过在“解决方案”视图中右键单击你的新项目，然后选择“选项”，打开项目属性。 选择“生成”部分中的“Android 应用程序”项。
   
    确保“包名称”的第一个字母为小写形式。
   
   > [!IMPORTANT]
   > 包名称的第一个字母必须为小写形式。 否则，在为以下的推送通知注册 **BroadcastReceiver** 和 **IntentFilter** 时，将收到应用程序清单错误。
   > 
   > 
   
      ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)
2. （可选）将“最低 Android 版本”设置为另一个 API 级别。
3. （可选）将“目标 Android 版本”设置为要针对的另一个 API 版本（必须是 API 级别 8 或更高）。

单击“确定”并关闭“项目选项”对话框。

### <a name="add-the-required-components-to-your-project"></a>将所需的组件添加到你的项目
Xamarin 组件应用商店中提供的 Google Cloud Messaging Client 可以简化在 Xamarin.Android 中支持推送通知的过程。

1. 在 Xamarin.Android 应用中右键单击“组件”文件夹，然后选择“获取更多组件”。
2. 搜索 **Azure 消息传送** 组件并将其添加到项目。
3. 搜索 **Google Cloud Messaging 客户端** 组件并将其添加到项目。

### <a name="set-up-notification-hubs-in-your-project"></a>在项目中设置通知中心
1. 收集有关 Android 应用和通知中心的以下信息：
   
   * **GoogleProjectNumber**：在 Google 开发人员门户上，从应用的概览中获取此项目编号值。 当你在门户上创建应用时，已事先记下了这个值。
   * **侦听连接字符串**：在 [Azure 经典门户]中的仪表板上，单击“查看连接字符串”。 复制此值的 *DefaultListenSharedAccessSignature* 连接字符串。
   * **中心名称**：这是你的中心在 [Azure 经典门户]上的名称。 例如 *mynotificationhub2*。
     
     为 Xamarin 项目创建 **Constants.cs** 类，并在该类中定义以下常量值。 将占位符替换为你自己的值。
     
       public static class Constants   {
     
           public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
       }
2. 将以下 using 语句添加到 **MainActivity.cs**：
   
        using Android.Util;
        using Gcm.Client;
3. 将一个实例变量添加到 `MainActivity` 类，用于在运行应用时显示警报对话框。
   
        public static MainActivity instance;
4. 在 **MainActivity** 类中创建以下方法：
   
        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);
   
            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }
5. 在 **MainActivity.cs** 的方法 `OnCreate` 中，初始化 `instance` 变量并添加对 `RegisterWithGCM` 的调用：
   
        protected override void OnCreate (Bundle bundle)
        {
            instance = this;
   
            base.OnCreate (bundle);
   
            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);
   
            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);
   
            RegisterWithGCM();
        }
6. 创建新类 **MyBroadcastReceiver**。
   
   > [!NOTE]
   > 下面，我们将演练如何从头开始创建 **BroadcastReceiver** 类。 但是，可以使用一种快捷方法来替代手动创建 **MyBroadcastReceiver.cs**：引用 **NotificationHubs 示例**随附的 Xamarin.Android 项目中的 [GcmService.cs][GitHub] 文件。 也可以从复制 **GcmService.cs** 并更改类名称着手。
   > 
   > 
7. 将以下 using 语句添加到 **MyBroadcastReceiver.cs** （引用前面添加的组件和程序集）：
   
        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;
8. 在 **MyBroadcastReceiver.cs** 中，在 **using** 语句和 **namespace** 声明之间添加以下权限请求：
   
        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
   
        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
9. 在 **MyBroadcastReceiver.cs** 中，更改 **MyBroadcastReceiver** 类以匹配以下内容：
   
        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };
   
            public const string TAG = "MyBroadcastReceiver-GCM";
        }
10. 在 **MyBroadcastReceiver.cs** 中添加名为 **PushHandlerService** 的一个类，该类从 **GcmServiceBase** 派生。 确保将 **Service** 属性应用到类：
    
         [Service] // Must use the service tag
         public class PushHandlerService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }
             private NotificationHub Hub { get; set; }
    
             public PushHandlerService() : base(Constants.SenderID)
                {
                 Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
             }
         }
11. **GcmServiceBase** 实现方法 **OnRegistered()**、**OnUnRegistered()**、**OnMessage()**、**OnRecoverableError()** 和 **OnError()**。 我们的 **PushHandlerService** 实现类必须重写这些方法，在响应与通知中心的交互时将激发这些方法。
12. 在 **PushHandlerService** 中使用以下代码重写 **OnRegistered()** 方法：
    
         protected override void OnRegistered(Context context, string registrationId)
         {
             Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
             RegistrationID = registrationId;
    
             createNotification("PushHandlerService-GCM Registered...",
                                 "The device has been Registered!");
    
             Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                         context);
             try
             {
                 Hub.UnregisterAll(registrationId);
             }
             catch (Exception ex)
             {
                 Log.Error(MyBroadcastReceiver.TAG, ex.Message);
             }
    
             //var tags = new List<string>() { "falcons" }; // create tags if you want
             var tags = new List<string>() {};
    
             try
             {
                 var hubRegistration = Hub.Register(registrationId, tags.ToArray());
             }
             catch (Exception ex)
             {
                 Log.Error(MyBroadcastReceiver.TAG, ex.Message);
             }
         }
    
    > [!NOTE]
    > 在上述 **OnRegistered()** 代码中，你应注意可指定标签来注册特定消息传送通道。
    > 
    > 
13. 在 **PushHandlerService** 中使用以下代码重写 **OnMessage** 方法：
    
        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");
    
            var msg = new StringBuilder();
    
            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }
    
            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }
14. 将以下 **createNotification** 和 **dialogNotify** 方法添加到 **PushHandlerService**，以便在收到通知时告诉用户。
    
    > [!NOTE]
    > Android 5.0 版与更新版本中的通知设计与以前的版本有极大的不同。 如果在 Android 5.0 或更高版本上测试，需要运行应用才能收到通知。 有关详细信息，请参阅 [Android 通知](http://go.microsoft.com/fwlink/?LinkId=615880)。
    > 
    > 
    
        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
    
            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));
    
            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
    
            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;
    
            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
    
            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }
    
        protected void dialogNotify(String title, String message)
        {
    
            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }
15. 重写抽象成员 **OnUnRegistered()**、**OnRecoverableError()** 和 **OnError()**，以便能够编译你的代码。
    
        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);
    
            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }
    
        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);
    
            return base.OnRecoverableError (context, errorId);
        }
    
        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

## <a name="run-your-app-in-the-emulator"></a>在模拟器中运行应用
如果在模拟器中运行此应用，请确保使用支持 Google API 的 Android 虚拟设备 (AVD)。

> [!IMPORTANT]
> 为了接收推送通知，你必须在 Android 虚拟设备上设置 Google 帐户。 （在模拟器中，导航到“设置”，然后单击“添加帐户”）。此外，请确保模拟器已连接到 Internet。
> 
> [!NOTE]
> Android 5.0 版与更新版本中的通知设计与以前的版本有极大的不同。 有关详细信息，请参阅 [Android 通知](http://go.microsoft.com/fwlink/?LinkId=615880)。
> 
> 

1. 从“工具”中，单击“打开 Android 模拟器管理器”，选择设备，然后单击“编辑”。
   
      ![][18]
2. 在“目标”中选择“Google API”，然后单击“确定”。
   
      ![][19]
3. 在顶部工具栏中，单击“运行”，然后选择应用。 这将启动模拟器并运行该应用程序。
   
   应用将从 GCM 检索 *registrationId* 并注册到通知中心。

## <a name="send-notifications-from-your-backend"></a>从后端发送通知
在 [Azure 经典门户] 中通过通知中心上的调试选项卡（如以下屏幕中所示）来发送通知，可以在应用中测试通知的接收情况。

![][30]

通常，推送通知是在后端服务（例如，移动服务，或者通过兼容库的 ASP.NET）中发送的。 如果你的后端没有可用的库，则你也可以使用 REST API 直接发送通知消息。

下面是你可能想要查看的有关发送通知的其他教程列表：

* ASP.NET：请参阅 [使用通知中心将通知推送到用户]。
* Azure 通知中心 Java SDK：有关从 Java 发送通知的信息，请参阅 [如何通过 Java 使用通知中心](notification-hubs-java-push-notification-tutorial.md) 。 这种方法已在 Eclipse for Android 开发环境中进行测试。
* PHP：请参阅 [如何通过 PHP 使用通知中心](notification-hubs-php-push-notification-tutorial.md)。

在本教程的后续小节中，你将使用 .NET 控制台应用和移动服务通过节点脚本来发送通知。

#### <a name="optional-send-notifications-by-using-a-net-app"></a>（可选）使用 .NET 应用发送通知
在本部分，你将使用 .NET 控制台应用发送通知

1. 创建新的 Visual C# 控制台应用程序：
   
      ![][20]
2. 在 Visual Studio 中，依次单击“工具”、“NuGet 包管理器”和“包管理器控制台”。
   
    这会在 Visual Studio 中显示“包管理器控制台”。
3. 在“包管理器控制台”窗口中，将“默认项目”设置为新的控制台应用程序项目，然后在控制台窗口中执行以下命令： 
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    这将使用 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet 包</a>添加对 Azure 通知中心 SDK 的引用。
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
4. 打开文件 Program.cs 并添加以下 `using` 语句：
   
        using Microsoft.Azure.NotificationHubs;
5. 在 `Program` 类中添加以下方法。 使用 *DefaultFullSharedAccessSignature* 连接字符串和 [Azure 经典门户]中的中心名称来更新占位符文本。
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }
6. 在 **Main** 方法中添加下列行：
   
         SendNotificationAsync();
         Console.ReadLine();
7. 按 F5 键以运行应用。 你应会在应用中收到通知。
   
      ![][21]

#### <a name="optional-send-notifications-by-using-a-mobile-service"></a>（可选）使用移动服务发送通知
1. 按照 [移动服务入门]操作。
2. 登录到 [Azure 经典门户]并选择你的移动服务。
3. 选择顶部的“计划程序”选项卡。
   
      ![][22]
4. 创建新的计划作业，插入名称，然后选择“按需”。
   
      ![][23]
5. 创建作业时，单击该作业名称。 然后单击顶部栏上的“脚本”选项卡。
6. 在你的计划程序函数中插入以下脚本。 确保将占位符替换为你先前获取的通知中心名称和 *DefaultFullSharedAccessSignature* 的连接字符串。 单击“保存” 。
   
        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );
7. 单击底部栏上的“运行一次”。 你应接收 toast 通知。

## <a name="next-steps"></a>后续步骤
在这个简单的示例中，你将通知广播到所有 Android 设备。 若要针对特定客户，请参考教程 [使用通知中心将通知推送到用户]。 如果要按兴趣组划分用户，可以阅读 [使用通知中心发送突发新闻]。 在[通知中心指南]和[针对 Android 的通知中心操作指南]中了解有关如何使用通知中心的详细信息。

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
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[移动服务入门]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure 经典门户]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知中心指南]: http://msdn.microsoft.com/library/jj927170.aspx
[针对 Android 的通知中心操作指南]: http://msdn.microsoft.com/library/dn282661.aspx

[使用通知中心将通知推送到用户]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中心发送突发新闻]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Google Cloud Messaging 客户端组件]: http://components.xamarin.com/view/GCMClient/
[Azure 消息传送组件]: http://components.xamarin.com/view/azure-messaging

