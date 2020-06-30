---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c13b7ee8c5c0a0d302e4822047ea60f9df120bf8
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112099"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>配置 Info.plist 和 Entitlements.plist

1. 确保已登录到“Visual Studio” > “首选项...” > “发布” > “Apple 开发人员帐户”中的“Apple 开发人员帐户”，并且已下载相应“证书”和“预配配置文件”     。 应已在前面的步骤中创建这些资产。

1. 在 PushDemo.iOS 中，打开 Info.plist，并确保 BundleIdentifier 与用于 [Apple 开发人员门户](https://developer.apple.com)中的各个预配配置文件的值匹配  。 BundleIdentifier 的格式为 ``com.<organization>.PushDemo``。

1. 在同一文件中，将“最低系统版本”设置为 13.0 。

    > [!NOTE]
    > 在本教程中，仅支持运行 iOS 13.0 及更高版本的设备，但你可以将其扩展为支持运行较早版本的设备。

1. 为 PushDemo.iOS 打开“项目选项”（双击项目） 。

1. 在“项目选项”的“生成”>“iOS 捆绑签名”下，确保选中“团队”下的开发人员帐户  。 然后，确保选中“自动管理签名”。这样就会自动选中签名证书和预配配置文件。

    > [!NOTE]
    > 如果未自动选择签名证书和预配配置文件，请选择“手动预配”，并单击“捆绑签名选项”  。 确保为“签名标识”选择“团队”，并为“调试”和“发布”配置的预配配置文件选择特定于 PushDemo 的预配配置文件，同时确保在这两种情况下都为“平台”选择 iPhone    。

1. 在 PushDemo.iOS 中，打开 Entitlements.plist，并确保“权利”选项卡中已选中“启用推送通知”   。然后，确保“源”选项卡中的“APS 环境”设置设为“开发”  。

### <a name="handle-push-notifications-for-ios"></a>处理 iOS 的推送通知

1. 控制 + 单击 PushDemo.iOS 项目，从“添加”菜单中选择“新建文件夹”，然后单击“添加”并使用“服务”作为“文件夹名称”     。

1. 按住“Control”的同时单击“服务”文件夹，然后从“添加”菜单中选择“新建文件...”    。

1. 选择“常规” > “空类”，输入 DeviceInstallationService.cs 作为“名称”，然后单击“新建”以添加以下实现  。

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > 此类提供一个唯一 ID（使用 [UIDevice.IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) 值）和通知中心注册有效负载。

1. 将新文件夹添加到名为“扩展”的 PushDemo.iOS 项目，然后使用以下实现将“空类”添加到名为 NSDataExtensions.cs 的文件夹。

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. 在 AppDelegate.cs 中，确保已将以下命名空间添加到文件顶部。

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. 为设备令牌缓存密钥添加常量。

    ```csharp
    const string CachedDeviceToken = "cached_device_token";
    ```

1. 添加专用属性及其各自的支持字段，以存储对 IPushDemoNotificationActionService、INotificationRegistrationService 和 IDeviceInstallationService 实现的引用  。

    ```csharp
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
    ```

1. 添加 RegisterForRemoteNotifications 方法以注册用户通知设置，然后使用 APNS 注册远程通知 。

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. 添加 CompleteRegistrationAsync 方法以设置 `IDeviceInstallationService.Token` 属性值。 刷新注册并缓存设备令牌（如果它自上次存储以来已发生更新）。

    ```csharp
    async Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();

        var cachedToken = await SecureStorage.GetAsync(CachedDeviceToken)
            .ConfigureAwait(false);

        if (!string.IsNullOrWhiteSpace(cachedToken) &&
            cachedToken.Equals(DeviceInstallationService.Token))
            return;

        await NotificationRegistrationService.RefreshRegistrationAsync()
            .ConfigureAwait(false);

        await SecureStorage.SetAsync(CachedDeviceToken, DeviceInstallationService.Token)
            .ConfigureAwait(false);
    }
    ```

1. 添加 ProcessNotificationActions 方法，用于处理 NSDictionary 通知数据并有条件地调用 NotificationActionService.TriggerAction  。

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. 替代 RegisteredForRemoteNotifications 方法，该方法将 deviceToken 参数传递到 CompleteRegistrationAsync 方法  。

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. 替代 ReceivedRemoteNotification 方法，该方法将 userInfo 参数传递到 ProcessNotificationActions 方法  。

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. 替代 FailedToRegisterForRemoteNotifications 方法以记录错误。

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > 这就是一个占位符。 对于生产场景，需要实现正确的日志记录和错误处理。

1. 更新 FinishedLaunching 方法，以便在对 `Forms.Init` 的调用传入特定于平台的 IDeviceInstallationService 实现后立即调用 `Bootstrap.Begin` 。

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. 在同一方法中，有条件地请求授权，并在 `Bootstrap.Begin` 后立即注册远程通知。

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. 仍然是在 FinishedLaunching 中，如果 options 参数包含可传入生成的 userInfo 对象的 UIApplication.LaunchOptionsRemoteNotificationKey，则在调用 `LoadApplication` 后立即调用 ProcessNotificationActions    。

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
