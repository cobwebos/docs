---
title: include 文件
description: include 文件包含用于创建后端 ASP .NET WebAPI 项目的代码。
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 6911f769b95967aac933dd9762263e7506aef4b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77193150"
---
## <a name="create-the-webapi-project"></a>创建 WebAPI 项目

后续部分讨论如何创建新的 ASP.NET WebAPI 后端。 此过程有三个主要目的：

- **对客户端进行身份验证**：添加消息处理程序，以便对客户端请求进行身份验证，并将用户与请求相关联。
- **使用 WebAPI 后端注册通知**：添加一个控制器来处理新的注册，使客户端设备能够接收通知。 经过身份验证的用户名将作为[标记](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md)自动添加到注册。
- **将通知发送到客户端**：添加一个控制器，以便用户触发安全推送到与标记关联的设备和客户端。

通过执行以下操作创建新的 ASP.NET WebAPI 后端：

> [!IMPORTANT]
> 如果使用 Visual Studio 2015 或更低版本，则在开始学习本教程之前，请确保已安装用于 Visual Studio 的最新版 NuGet 包管理器。
>
>若要进行检查，请启动 Visual Studio。 在“工具”  菜单上，选择“扩展和更新”  。 在你的 Visual Studio 版本中搜索“NuGet 包管理器”，确保你的版本为最新  。 如果你的版本不是最新版本，请卸载它，然后重新安装 NuGet 包管理器。

![][B4]

> [!NOTE]
> 请确保已安装 Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) 以便进行网站部署。

1. 启动 Visual Studio 或 Visual Studio Express。

2. 选择“服务器资源管理器”并登录到 Azure 帐户  。 若要在帐户中创建网站资源，必须先登录。

3. 在 Visual Studio 中，右键单击 Visual Studio 解决方案，指向“添加”，然后单击“新建项目”   。
4. 展开“Visual C#”，选择“Web”，然后单击“ASP.NET Web 应用程序”    。

5. 在“名称”  框中，键入 **AppBackend**，然后选择“确定”  。

    ![“新建项目”窗口][B1]

6. 在“新建 ASP.NET 项目”  窗口中，选择“Web API”  复选框，然后选择“确定”  。

    ![“新建 ASP.NET 项目”窗口][B2]

7. 在“配置 Microsoft Azure Web 应用”窗口中，选择一个订阅，然后在“应用服务计划”列表中，执行以下任一操作   ：

    * 选择已创建的应用服务计划。
    * 选择“创建新的应用服务计划”，然后新建一个应用服务计划  。

   在本教程中，不需要使用数据库。 选择应用服务计划后，选择“确定”以创建项目  。

    ![“配置 Microsoft Azure Web 应用”窗口][B5]

    如果没有看到用于配置应用服务计划的此页面，请继续学习本教程。 可以在稍后发布应用时对其进行配置。 

## <a name="authenticate-clients-to-the-webapi-backend"></a>在 WebAPI 后端对客户端进行身份验证

在本部分中，将为新的后端创建名为“AuthenticationTestHandler”的新消息处理程序类  。 此类派生自 [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) 并已添加为消息处理程序，使它可以处理传入后端的所有请求。

1. 在“解决方案资源管理器”中，右键单击“AppBackend”项目，依次选择“添加”、“类”    。
2. 将新类命名为 **AuthenticationTestHandler.cs**，并选择“添加”生成该类  。 为简单起见，此类将通过使用*基本身份验证*对用户进行身份验证。 请注意，应用可以使用任何身份验证方案。
3. 在 AuthenticationTestHandler.cs 中，添加以下 `using` 语句：

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. 在 AuthenticationTestHandler.cs 中，将 `AuthenticationTestHandler` 类定义替换为以下代码：

    当以下三个条件都成立时，此处理程序授权请求：

   * 请求包含 *Authorization* 标头。
   * 请求使用*基本*身份验证。
   * 用户名字符串和密码字符串是相同的字符串。

   否则，会拒绝该请求。 此身份验证不是真正的身份验证和授权方法。 它只是本教程中一个简单的示例。

   如果请求消息已经过 `AuthenticationTestHandler` 的身份验证和授权，则基本身份验证用户将附加到 [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx) 上的当前请求。 稍后，另一个控制器 (RegisterController) 会使用 HttpContext 中的用户信息，将[标记](https://msdn.microsoft.com/library/azure/dn530749.aspx)添加到通知注册请求。

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (VerifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool VerifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > 安全说明：`AuthenticationTestHandler` 类不提供真正的身份验证。 它仅用于模拟基本身份验证并且是不安全的。 必须在生产应用程序和服务中实现安全的身份验证机制。
5. 若要注册消息处理程序，请在 **App_Start/WebApiConfig.cs** 类中 `Register` 方法的末尾添加以下代码：

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. 保存所做更改。

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>使用 WebAPI 后端注册通知

在本部分中，要将新的控制器添加到 WebAPI 后端来处理请求，以使用通知中心的客户端库为用户和设备注册通知。 控制器将为已由 `AuthenticationTestHandler` 验证并附加到 HttpContext 的用户添加用户标记。 该标记采用以下字符串格式：`"username:<actual username>"`。

1. 在“解决方案资源管理器”中，右键单击“AppBackend”项目，并选择“管理 NuGet 包”   。

2. 在左窗格中，选择“联机”  ，然后在“搜索”  框中，键入 **Microsoft.Azure.NotificationHubs**。

3. 在结果列表中，选择“Microsoft Azure 通知中心”，然后选择“安装”   。 完成安装后，关闭“NuGet 程序包管理器”窗口。

    此操作会使用 [Microsoft.Azure.Notification Hubs NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)添加对 Azure 通知中心 SDK 的引用。

4. 创建新的类文件，以表示与用于发送通知的通知中心的连接。 在“解决方案资源管理器”中，右键单击“模型”文件夹，选择“添加”，并选择“类”。    将新类命名为 **Notifications.cs**，并选择“添加”生成该类  。

    ![“添加新项”窗口][B6]

5. 在 Notifications.cs 中，在文件顶部添加以下 `using` 语句：

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. 将 `Notifications` 类定义替换为以下代码，并将两个占位符替换为通知中心的连接字符串（具有完全访问权限）和中心名称（可在 [Azure 门户](https://portal.azure.com)中找到）：

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > 输入中心的名称和 DefaultFullSharedAccessSignature，然后继续   。 
    
7. 接下来将创建一个名为 **RegisterController** 的新控制器。 在“解决方案资源管理器”中，右键单击“控制器”文件夹，选择“添加”，并选择“控制器”。   

8. 选择“Web API 2 控制器 - 空”  ，并选择“添加”  。

    ![“添加基架”窗口][B7]

9. 在“控制器名称”  框中，键入 **RegisterController** 以命名新类，并选择“添加”  。

    ![“添加控制器”窗口][B8]

10. 在 RegisterController.cs 中，添加以下 `using` 语句：

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. 在 `RegisterController` 类定义中添加以下代码。 在此代码中，将为已附加到 HttpContext 的用户添加用户标记。 添加的消息筛选器 `AuthenticationTestHandler` 将对该用户进行身份验证并将其附加到 HttpContext。 还可以通过添加可选复选框来验证用户是否有权注册以获取请求标记。

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. 保存所做更改。

## <a name="send-notifications-from-the-webapi-backend"></a>从 WebAPI 后端发送通知

在本部分中，添加一个新的控制器，使客户端设备可以发送通知。 通知基于在 ASP.NET WebAPI 后端中使用 Azure 通知中心 .NET 库的用户名标记。

1. 以在前面的部分中创建 **RegisterController** 的相同方式创建另一个名为 **NotificationsController** 的新控制器。

2. 在 NotificationsController.cs 中，添加以下 `using` 语句：

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. 在 **NotificationsController** 类中添加以下方法：

    此代码会发送基于平台通知服务 (PNS) `pns` 参数的通知类型。 `to_tag` 的值用于设置消息中的 *username* 标记。 此标记必须与活动的通知中心注册的用户名标记相匹配。 将从 POST 请求正文提取通知消息，并根据目标 PNS 将其格式化。

    通知受多种格式支持，具体取决于受支持设备用来接收通知的 PNS。 例如，在 Windows 设备上，可能会将 [toast 通知与其他 PNS 不直接支持的 WNS 配合使用](https://msdn.microsoft.com/library/windows/apps/br230849.aspx)。 在这种情况下，后端需要将通知格式化为打算使用的设备 PNS 所支持的通知。 然后针对 [NotificationHubClient 类](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)使用相应的发送 API。

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. 若要运行应用程序并确保到目前为止操作的准确性，请选择 **F5** 键。 应用将打开 Web 浏览器，并且将显示在 ASP.NET 主页上。

## <a name="publish-the-new-webapi-backend"></a>发布新的 WebAPI 后端

接下来会将此应用部署到 Azure 网站，以便可以从任意设备访问它。

1. 右键单击 **AppBackend** 项目，并选择“发布”  。

2. 选择“Microsoft Azure 应用服务”作为发布目标，然后选择“发布”  。 “创建应用服务”窗口将打开。 可以在这里创建在 Azure 中运行 ASP.NET Web 应用所需的全部 Azure 资源。

    ![Microsoft Azure 应用服务磁贴][B15]

3. 在“创建应用服务”窗口中，选择 Azure 帐户  。 选择“更改类型”   > “Web 应用”  。 保留默认的“Web 应用名称”，然后依次  选择“订阅”、“资源组”和“应用服务计划”。   

4. 选择“创建”  。

5. 记下“摘要”部分的“站点 URL”属性。   此 URL 是本教程中稍后提到的*后端终结点*。

6. 选择“发布”  。

完成向导后，它会将 ASP.NET Web 应用发布到 Azure，然后在默认浏览器中打开该应用。  可以在 Azure 应用服务中查看应用程序。

URL 使用前面指定的 Web 应用名称，其格式为 http://<app_name>.azurewebsites.net。

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
