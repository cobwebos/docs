---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: fc479522b3fd436ff02ff6b8985bdeddb66da90a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448760"
---
### <a name="create-the-xamarinforms-solution"></a>创建 Xamarin.Forms 解决方案

1. 在 Visual Studio 中，使用“空白窗体应用”作为模板创建一个新的 Xamarin.Forms 解决方案，并输入 PushDemo 作为项目名称  。

    > [!NOTE]
    > 在“配置空白窗体应用”对话框中，确保“组织标识符”与之前使用的值相匹配，并确保同时选中 Android 和 iOS 目标   。

1. 按住 Control 的同时单击 PushDemo 解决方案，然后选择“更新 NuGet 包” 。
1. 按住 Control 的同时单击 PushDemo 解决方案，然后选择“管理 NuGet 包...” 。
1. 搜索“Newtonsoft.Json”并确保将其选中。
1. 单击“添加包”，然后在系统提示接受许可条款时单击“接受” 。
1. 在每个目标平台上构建并运行应用 (Command + Enter)，以测试模板化应用在设备上的运行情况 。

### <a name="implement-the-cross-platform-components"></a>实现跨平台组件

1. 按住 Control 的同时单击 PushDemo 项目，从“添加”菜单中选择“新建文件夹”，然后单击“添加”并使用“模型”作为“文件夹名称”     。

1. 按住 Control 的同时单击“模型”文件夹，然后从“添加”菜单中选择“新建文件...”    。

1. 选择“常规” > “空类”，输入 DeviceInstallation.cs，然后添加以下实现 。

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. 使用以下实现将“空枚举”添加到名为 PushDemoAction.cs 的“模型”文件夹 。

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. 将新文件夹添加到名为“服务”的 PushDemo 项目，然后使用以下实现将“空类”添加到名为 ServiceContainer.cs 的文件夹。

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 这是 [XamCAT](https://github.com/xamcat/mobcat-library) 存储库中 [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) 类的精简版本。 它将用作轻量 IoC（控制反转）容器。

1. 将“空接口”添加到名为 IDeviceInstallationService.cs 的“服务”文件夹中，然后添加以下代码。

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > 稍后将由每个目标实现并引导此接口，以提供特定于平台的功能和后端服务所需的 DeviceInstallation 信息。

1. 将另一个“空接口”添加到名为 INotificationRegistrationService.cs 的“服务”文件夹中，然后添加以下代码 。  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > 这将处理客户端和后端服务之间的交互。

1. 将另一个“空接口”添加到名为 INotificationActionService.cs 的服务”文件夹中，然后添加以下代码 。  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > 这是集中处理通知操作的简单机制。

1. 使用以下实现将“空接口”添加到名为 IPushDemoNotificationActionService.cs 的“服务”文件夹，该文件夹派生自 INotificationActionService  。  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > 此类型特定于 PushDemo 应用程序，并使用 PushDemoAction 枚举来标识以强类型方式触发的操作 。

1. 将“空类”添加到名为 NotificationRegistrationService.cs 的“服务”文件夹，并使用以下代码实现 INotificationRegistrationService  。

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > 仅当你选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分时，才需要 apiKey 参数。

1. 将“空类”添加到名为 PushDemoNotificationActionService.cs 的“服务”文件夹，并使用以下代码实现 IPushDemoNotificationActionService  。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. 使用以下实现将“空类”添加到名为 Config.cs 的 PushDemo 项目 。  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > 这是将机密从源代码管理中排除的简单方法。 可以将这些值替换为自动生成的一部分，或使用本地部分类替代它们。 你将在下一步中执行此操作。
    >
    > 仅当你选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分时，才需要 ApiKey 字段。

1. 使用以下实现将另一个“空类”添加到 PushDemo 项目中，这一次，项目的名称为为 Config.local_secrets.cs 。  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > 将占位符值替换为你自己的值。 应在构建后端服务时记下这些值。 **API 应用** URL 应为 ``https://<api_app_name>.azurewebsites.net/``。 请务必将 ``*.local_secrets.*`` 添加到 gitignore 文件，以避免提交此文件。
    >
    > 仅当你选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分时，才需要 ApiKey 字段。

1. 使用以下实现将“空类”添加到名为 Bootstrap.cs 的 PushDemo 项目 。  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > 当应用启动时，每个平台都会调用 Begin 方法，并传入特定于平台的 IDeviceInstallationService 实现 。
    >
    > 仅当你选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分时，才需要 NotificationRegistrationService apiKey 构造函数参数 。

### <a name="implement-the-cross-platform-ui"></a>实现跨平台 UI

1. 在 PushDemo 项目中，打开 MainPage.xaml 并将 StackLayout 控件替换为以下项  。

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. 现在，在 MainPage.xaml.cs 中添加“只读”支持字段，以存储对 INotificationRegistrationService 实现的引用  。

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. 在 MainPage 构造函数中，使用 ServiceContainer 解析 INotificationRegistrationService 实现，并将其分配给 notificationRegistrationService 支持字段  。

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. 调用相应的 Register/Deregister 方法，为 RegisterButton 和 DeregisterButton 按钮“已单击”事件实现事件处理程序    。

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. 现在，在 App.xaml.cs 中，确保引用以下命名空间。

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. 为 IPushDemoNotificationActionService ActionTriggered 事件实现事件处理程序 。

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. 在“应用”构造函数中，使用 ServiceContainer 解析 IPushNotificationActionService 实现，并订阅 IPushDemoNotificationActionService ActionTriggered 事件    。

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > 这只是为了演示推送通知操作的接收和传播。 通常情况下，将以无提示方式处理这些操作（例如导航到特定视图或刷新某些数据），而不是通过根页面（在本例中为 MainPage）显示警报 。
