---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095182"
---
### <a name="create-a-web-project"></a>创建 Web 项目

1. 在 Visual Studio 中，选择“文件” > “新建解决方案”  。

1. 依次选择“.NET Core” > “应用” > “ASP.NET Core” > “API” > “下一步”    。
  
1. 在“配置新的 ASP.NET Core Web API”对话框中，选择 .NET Core 3.1 作为“目标框架”  。

1. 输入“PushDemoApi”作为项目名称，然后选择“创建” 。

1. 启动调试 (Command + Enter) 来测试模板化应用 。

    > [!NOTE]
    > 模板化应用配置为使用 WeatherForecastController 作为 launchUrl。 这是在“属性” > “launchSettings.json”中设置的 。  
    >
    > 如果系统提示“找到的开发证书无效”消息：
    >
    > 1. 单击“是”同意运行“dotnet dev-certs https”工具来解决此问题。 然后“dotnet dev-certs https”工具会提示你输入证书的密码和密钥链的密码。
    >
    > 1. 当系统提示“安装并信任新证书”时，单击“是”，然后输入密钥链的密码 。

1. 展开“控制器”文件夹，然后删除 WeatherForecastController.cs 。

1. 删除 WeatherForecast.cs。

1. 按住 Control 的同时单击 PushDemoApi 项目，然后从“添加”菜单中选择“新建文件...”    。

1. 使用[机密管理器工具](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager)设置本地配置值。 将机密与解决方案分离可确保它们不会终止在源代码管理中。 打开“终端”，然后转到项目文件的目录，并运行以下命令：

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    将占位符值替换为自己的通知中心名称和连接字符串值。 你已在[创建通知中心](#create-a-notification-hub)部分中记下了这些值。 否则，可以在 [Azure](https://portal.azure.com) 中查找这些值。

    **NotificationsHub:Name**：  
    请参阅“概述”顶部“基础”摘要中的“名称” 。  

    **NotificationHub:ConnectionString**：  
    请参阅“访问策略”中的 DefaultFullSharedAccessSignature

    > [!NOTE]
    > 对于生产方案，可以查看 [Azure KeyVault](https://azure.microsoft.com/services/key-vault) 等选项，以安全地存储连接字符串。 为简单起见，机密会添加到 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)应用程序设置。

### <a name="authenticate-clients-using-an-api-key-optional"></a>使用 API 密钥对客户端进行身份验证（可选）

API 密钥的安全性虽然不如令牌，但它也可以满足本教程的需要。 可通过 [ASP.NET 中间件](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1)轻松配置 API 密钥。

1. 将“API 密钥”添加到本地配置值。

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > 应将占位符值替换为你自己的值，并对其进行记录。

1. 按住 Control 的同时单击 PushDemoApi 项目，从“添加”菜单中选择“新建文件夹”，然后单击“添加”，并使用“身份验证”作为文件夹名称     。

1. 按住 Control 的同时单击“身份验证”文件夹，然后从“添加”菜单中选择“新建文件...”    。

1. 选择“常规” > “空类”，输入“ApiKeyAuthOptions.cs”作为名称，然后单击“新建”添加以下实现  。

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. 将另一个“空类”添加到名为 ApiKeyAuthHandler.cs 的“身份验证”文件夹中，然后添加以下实现。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > [身份验证处理程序](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler)是实现方案行为的类型，在本例中为自定义 API 密钥方案。

1. 将另一个“空类”添加到名为 ApiKeyAuthenticationBuilderExtensions.cs 的“身份验证”文件夹中，然后添加以下实现。

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > 此扩展方法简化了 Startup.cs 中的中间件配置代码，使其更具有可读性并且更加通俗易懂。

1. 在 Startup.cs 中，更新 ConfigureServices 方法，以便在对 services.AddControllers 方法的调用下配置 API 密钥身份验证  。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. 继续在 Startup.cs 中更新 Configure 方法，以便对应用的 IApplicationBuilder 调用 UseAuthentication 和 UseAuthorization扩展方法    。 请确保在 UseRouting 之后且 app.UseEndpoints 之前调用这些方法 。

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > 调用 UseAuthentication 将注册中间件，该中间件使用以前注册的身份验证方案（来自 ConfigureServices） 。 必须在依赖于要进行身份验证的用户的任何中间件之前，调用此方法。

### <a name="add-dependencies-and-configure-services"></a>添加依赖项并配置服务

ASP.NET Core 支持[依赖项注入 (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) 软件设计模式，这是一种在类与其依赖项之间实现[控制反转 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的方法。  

使用的通知中心和[用于后端操作的通知中心 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) 封装在服务中。 该服务通过适当的抽象进行注册和提供。

1. 按住 Control 的同时单击“依赖项”文件夹，然后选择“管理 NuGet 包...”   。

1. 搜索 Microsoft.Azure.NotificationHubs 并确保选中它。

1. 单击“添加包”，然后在系统提示接受许可条款时单击“接受” 。

1. 按住 Control 的同时单击 PushDemoApi 项目，从“添加”菜单中选择“新建文件夹”，然后单击“添加”并使用“模型”作为文件夹名称     。

1. 按住 Control 的同时单击“模型”文件夹，然后从“添加”菜单中选择“新建文件...”    。

1. 选择“常规” > “空类”，输入 PushTemplates.cs 作为名称，然后单击“新建”添加以下实现  。

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > 此类包含此方案所需的泛型和无提示通知的标记化通知有效负载。 有效负载在[安装](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet)外定义，以便无需通过服务更新现有安装即可进行试验。 以这种方式处理对安装的更改超出了本教程的范围。 对于生产环境，请考虑使用[自定义模板](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。

1. 选择“常规” > “空类”，输入 DeviceInstallation.cs 作为“名称”，然后单击“新建”添加以下实现  。

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. 将另一个“空类”添加到名为 NotificationRequest.cs 的“模型”文件夹中，然后添加以下实现。

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. 将另一个“空类”添加到名为 NotificationHubOptions.cs 的“模型”文件夹中，然后添加以下实现 。

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. 将一个新文件夹添加到名为“服务”的 PushDemoApi 项目。

1. 将“空接口”添加到名为 INotificationService.cs 的“服务”文件夹，然后添加以下实现 。

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. 将“空类”添加到名为 NotificationHubsService.cs 的“服务”文件夹中，然后添加以下代码以实现 INotificationService 接口 ：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > 提供给 SendTemplateNotificationAsync 的标记表达式限制为包含 20 个标记。 大多数运算符的限制数量为 6 个，但在本例中，表达式仅包含 OR (||)。 如果请求中的标记超过 20 个，则必须将它们拆分为多个请求。 有关更多详细信息，请参阅[路由和标记表达式](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396)文档。

1. 在 Startup.cs 中，更新 ConfigureServices 方法，以将 NotificationHubsService 添加为 INotificationService 的单一实现   。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>创建通知 API

1. 按住 Control 的同时单击“控制器”文件夹，然后从“添加”菜单中选择“新建文件...”    。

1. 选择“ASP.NET Core” > “Web API 控制器类”，输入 NotificationsController 作为名称，然后单击“新建”  。

1. 将以下命名空间添加到文件顶部。

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. 更新模板化控制器，使其从 ControllerBase 派生，并通过 ApiController 属性进行修饰 。

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > “控制器”基类为视图提供支持，但本例中不需要它，因此可以改为使用 ControllerBase 。

1. 如果选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分，则还应该使用 Authorize 特性来修饰 NotificationsController 。

    ```cs
    [Authorize]
    ```

1. 更新构造函数以接受 INotificationServic 的注册实例作为参数，并将其分配给只读成员。

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. 在 launchSettings.json（位于“属性”文件夹内）中，将 launchUrl 从 `weatherforecast` 更改为 api/notifications，以匹配在 RegistrationsController Route 特性中指定的 URL   。

1. 启动调试 (Command + Enter)，验证应用是否正在使用新的 NotificationsController 并返回“401 未授权”状态   。

    > [!NOTE]
    > Visual Studio 可能不会在浏览器中自动启动该应用。 此时将使用 [Postman](https://www.postman.com/downloads) 来测试 API。

1. 在新的 [Postman](https://www.postman.com/downloads) 选项卡上，将请求设置为“GET”并在下面输入地址 。

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > Localhost 地址应与“属性” > “launchSettings.json”中找到的 applicationUrl 值匹配  。 默认值应为 `https://localhost:5001;http://localhost:5000`，但是如果收到 404 响应，则需要验证这一点。

1. 如果选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分，请确保将请求标头配置为包含 apikey 值。

   | 密钥                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. 单击“发送”按钮。

    > [!NOTE]
    > 你应收到“200 OK”状态，其中包含一些 JSON 内容 。
    >
    > 如果收到“SSL 证书验证”警告，则可以在“设置”中关闭请求 SSL 证书验证 [Postman](https://www.postman.com/downloads) 设置  。

1. 将模板化类方法替换为以下代码。

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>创建 API 应用

现在，在 [Azure 应用服务](https://docs.microsoft.com/azure/app-service/)中创建 [API 应用](https://azure.microsoft.com/services/app-service/api/)，以便托管后端服务。  

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 单击“创建资源”，搜索并选择“API 应用”，然后单击“创建”  。

1. 更新以下字段，然后单击“创建”。

    **应用名称：**  
    输入 API 应用的全局唯一名称

    **订阅：**  
    选择在其中创建了通知中心的同一目标订阅。

    **资源组：**  
    选中在其中创建了通知中心的同一资源组。

    **应用服务计划/位置：**  
    创建新的应用服务计划  

    > [!NOTE]
    > 将默认选项更改为包含 SSL 支持的计划。 否则，在使用移动应用时，需要执行适当的步骤，以防止 http 请求被阻止。

    **Application Insights：**  
    保留建议的选项（将使用该名称创建新资源）或选择现有资源。

1. 预配 API 应用后，导航到该资源。

1. 记下“概述”顶部“基础信息”摘要中的 URL 属性  。 此 URL 是“后端终结点”，本教程后面的部分中会用到它。

    > [!NOTE]
    > URL 使用前面指定的 API 应用名称，其格式为 `https://<app_name>.azurewebsites.net`。

1. 从列表选择“配置”（在“设置”下） 。  

1. 对于下面的每个设置，单击“新建应用程序设置”，输入名称和值，然后单击“确定”   。

   | 名称                               | 值                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > 这些设置与你之前在用户设置中定义的设置相同。 应该可以复制将这些设置复制过来。 仅当你选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分时，才需要 Authentication:ApiKey 设置。 对于生产方案，可以查看 [Azure KeyVault](https://azure.microsoft.com/services/key-vault) 等选项。 在本例中，为简单起见，这些设置已作为应用程序设置添加。

1. 添加所有应用程序设置后，依次单击“保存”和“继续” 。

### <a name="publish-the-backend-service"></a>发布后端服务

接下来，需要将应用部署到 API 应用，以便可以从任意设备访问它。  

1. 将配置从“调试”更改为“发布”（如果尚未这样做） 。

1. 按住 Control 的同时单击 PushDemoApi 项目，然后从“发布”菜单中选择“发布到 Azure...”    。

1. 如果系统提示进行身份验证，请遵循身份验证流。 使用之前在[创建 API 应用](#create-the-api-app)部分中使用的帐户。

1. 从列表中选择之前创建的“Azure 应用服务 API 应用”作为发布目标，然后单击“发布” 。

完成向导后，它会将应用发布到 Azure，然后打开该应用。 记下 URL（如果尚未这样做）。 此 URL 是“后端终结点”，本教程后面的部分中会用到它。

### <a name="validating-the-published-api"></a>验证已发布的 API

1. 在 [Postman](https://www.postman.com/downloads) 中打开新选项卡，将请求设置为“POST”并在下面输入地址 。 将占位符替换为在之前的[发布后端服务](#publish-the-backend-service)部分中记下的基址。

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > 基址的格式应为 ``https://<app_name>.azurewebsites.net/``

1. 如果选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分，请确保将请求标头配置为包含 apikey 值。

   | 密钥                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. 为“正文”选择“原始”选项，接下来从格式选项列表中选择“JSON”，然后包含一些占位符 JSON 内容   ：

    ```json
    {}
    ```

1. 单击“Send”。

    > [!NOTE]
    > 你应收到来自服务的“400 错误请求”状态。

1. 再次执行步骤 1-4，但这次指定请求终结点以验证是否收到相同的“400 错误请求”响应。

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> 尚无法使用有效的请求数据测试 API，因为此操作需要来自客户端移动应用的特定于平台的信息。
