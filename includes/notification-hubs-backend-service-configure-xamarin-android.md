---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c919cfce3d868a81f28eb8172314e9639387e933
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112010"
---
### <a name="validate-package-name-and-permissions"></a>验证包名称和权限

1. 在 PushDemo.Android 中，打开“项目选项”，然后从“生成”部分打开“Android 应用程序”   。

1. 检查“包名称”是否与 [Firebase 控制台](https://console.firebase.google.com) PushDemo 项目中使用的值匹配 。 “包名称”的格式为 ``com.<organization>.pushdemo``。

1. 将“最低 Android 版本”设置为“Android 8.0 (API 级别 26)”，将“目标 Android 版本”设置为最新 API 级别   。

    > [!NOTE]
    > 在本教程中，仅支持运行 API 级别 26 及更高级别的设备，但可以将其扩展为支持运行较早版本的设备。

1. 确保在“所需权限”下启用 INTERNET 和 READ_PHONE_STATE 权限  。

1. 单击 **“确定”**

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>添加 Xamarin Google Play Services Base 和 Xamarin.Firebase.Messaging 包

1. 在 PushDemo.Android 中，按住“Control”的同时单击“包”文件夹，然后选择“管理 NuGet 包...”    。

1. 搜索 Xamarin.GooglePlayServices.Base（而非 Basement），并确保已将其选中 。

1. 搜索 Xamarin.Firebase.Messaging，并确保已将其选中。

1. 单击“添加包”，然后在系统提示接受许可条款时单击“接受”  。

### <a name="add-the-google-services-json-file"></a>添加 Google Services JSON 文件

1. 按住“Control”的同时单击 `PushDemo.Android` 项目，然后从“添加”菜单中选择“现有文件...”   。

1. 选择之前在 [Firebase 控制台](https://console.firebase.google.com)中设置 PushDemo 项目时下载的 google-services.json 文件，然后单击“打开” 。

1. 出现提示时，选择“将文件复制到目录”。

1. 在 `PushDemo.Android` 项目中，按住“Control”的同时单击 google-services.json 文件，然后确保将 GoogleServicesJson 设置为“生成操作”  。

### <a name="handle-push-notifications-for-android"></a>处理 Android 的推送通知

1. 按住“Control”的同时单击 `PushDemo.Android` 项目，从“添加”菜单中选择“新建文件夹”，然后单击“添加”并使用“服务”作为“文件夹名称”    。

1. 按住“Control”的同时单击“服务”文件夹，然后从“添加”菜单中选择“新建文件...”    。

1. 选择“常规” > “空类”，输入 DeviceInstallationService.cs 作为“名称”，然后单击“新建”以添加以下实现  。

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > 此类提供一个唯一 ID（使用 [Secure.AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)）作为通知中心注册有效负载的一部分。

1. 将另一个“空类”添加到名为 PushNotificationFirebaseMessagingService.cs 的“服务”文件夹中，然后添加以下实现 。

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. 在 MainActivity.cs 中，确保已将以下命名空间添加到文件顶部。

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. 在 MainActivity.cs 中，将 LaunchMode 设置为 SingleTop，以便在打开时不会再次创建 MainActivity   。

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. 添加专用属性和相应的支持字段，以存储对 IPushNotificationActionService 和 IDeviceInstallationService 实现的引用 。

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. 实现 IOnSuccessListener 接口以检索和存储 Firebase 令牌 。

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. 添加一个名为 ProcessNotificationActions 的新方法，该方法将检查给定“意向”是否具有一个名为“操作”的额外值 。 使用 IPushDemoNotificationActionService 实现在一定条件下触发该操作。

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. 替代 OnNewIntent 方法以调用 CheckIntentForNotificationActions 方法 。

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > 由于“活动”的 LaunchMode 设置为 SingleTop，因此会通过 OnNewIntent 方法（而不是 OnCreate 方法）向现有“活动”实例发送一个“意向”，因此必须同时在 OnCreate 和 OnNewIntent 方法中处理传入意向        。

1. 更新 OnCreate 方法，以便在对 `base.OnCreate` 的调用传入特定于平台的 IDeviceInstallationService 实现后立即调用 `Bootstrap.Begin` 。

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. 在同一方法中，在调用 `Bootstrap.Begin` 后立即有条件地在 FirebaseApp 实例上调用 GetInstanceId，从而将 MainActivity 添加为 IOnSuccessListener   。

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. 仍然是在 OnCreate 中，在对 `LoadApplication` 的调用传入当前“意向”后立即调用 ProcessNotificationActions  。

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> 必须在每次运行应用时重新注册该应用，并从调试会话中停止该应用以继续接收推送通知。
