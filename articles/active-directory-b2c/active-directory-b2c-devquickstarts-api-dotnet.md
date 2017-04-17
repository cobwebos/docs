---
title: Azure AD B2C | Microsoft Docs
description: "如何使用 Azure Active Directory B2C 构建 .NET Web API，以及使用 OAuth 2.0 身份验证访问令牌保护该 API。"
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: d67043f2e0a062796f4d6167b28774ce494027c2
ms.lasthandoff: 04/10/2017


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C：构建 .NET Web API

借助 Azure Active Directory (Azure AD) B2C，可以使用 OAuth 2.0 访问令牌来保护 Web API。 客户端应用可以使用这些令牌对 API 进行身份验证。 本文说明如何创建一个 .NET MVC“待办事项列表”API，使客户端应用程序的用户能够执行 CRUD 任务。 Web API 使用 Azure AD B2C 进行保护，只允许经过身份验证的用户管理其待办事项列表。

## <a name="create-an-azure-ad-b2c-directory"></a>创建 Azure AD B2C 目录

只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后继续执行本指南中的步骤。

> [!NOTE]
> 客户端应用程序和 Web API 必须使用相同的 Azure AD B2C 目录。
>

## <a name="create-a-web-api"></a>创建 Web API

接下来，需要在 B2C 目录中创建 Web API 应用。 此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 若要创建应用，请遵循 [这些说明](active-directory-b2c-app-registration.md)。 请务必：

* 在应用程序中包含一个 **Web 应用**或 **Web API**。
* 为 Web 应用使用“重定向 URI”`https://localhost:44332/`。 这是用于本代码示例的 Web 应用客户端的默认位置。
* 复制分配给应用的 **应用程序 ID** 。 稍后需要用到此信息。
* 在“应用 ID URI”中输入应用标识符。
* 通过“发布范围”菜单添加权限。

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略

在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 需要创建一个策略才能与 Azure AD B2C 通信。 建议根据[策略参考文章](active-directory-b2c-reference-policies.md)中所述结合使用注册/登录策略。 创建策略时，请务必：

* 在策略中，选择“显示名称”和其他注册属性。
* 针对每个策略选择“显示名称”和“对象 ID”声明作为应用程序声明。 也可以选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 稍后需要用到策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

成功创建策略后，可以开始构建应用。

## <a name="download-the-code"></a>下载代码

本教程的代码在 [GitHub](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi) 上维护。 可运行以下命令来克隆示例：

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

下载示例代码后，打开 Visual Studio .sln 文件开始处理。 解决方案文件包含两个项目：`TaskWebApp` 和 `TaskService`。 `TaskWebApp` 是用户交互的 MVC Web 应用程序。 `TaskService` 是应用的后端 Web API，存储每个用户的待办事项列表。 本文仅讨论 `TaskService` 应用程序。 若要了解如何使用 Azure AD B2C 构建 `TaskWebApp`，请参阅 [.NET Web 应用教程](active-directory-b2c-devquickstarts-web-dotnet-susi.md)。

### <a name="update-the-azure-ad-b2c-configuration"></a>更新 Azure AD B2C 配置

我们的示例配置为使用演示租户的策略和客户端 ID。 如果你想要使用自己的租户，需要执行以下操作：

1. 打开 `TaskService` 项目中的 `web.config`，然后
    * 将 `ida:Tenant` 的值替换为租户名称
    * 将 `ida:ClientId` 的值替换为 Web API 应用程序 ID
    * 将 `ida:SignUpSignInPolicyId` 的值替换为“注册或登录”策略名称

2. 打开 `TaskWebApp` 项目中的 `web.config`，然后
    * 将 `ida:Tenant` 的值替换为租户名称
    * 将 `ida:ClientId` 的值替换为 Web 应用的应用程序 ID
    * 将 `ida:ClientSecret` 的值替换为 Web 应用机密密钥
    * 将 `ida:SignUpSignInPolicyId` 的值替换为“注册或登录”策略名称
    * 将 `ida:EditProfilePolicyId` 的值替换为“编辑配置文件”策略名称
    * 将 `ida:ResetPasswordPolicyId` 的值替换为“重置密码”策略名称


## <a name="secure-the-api"></a>保护 API

如果某个客户端调用你的 API，你可以使用 OAuth 2.0 持有者令牌来保护 API（例如 `TaskService`）。 这可以确保仅当向 API 发出的每个请求包含持有者令牌时，该请求才有效。 API 可以使用适用于 .NET 的开放式 Web 接口 (OWIN) 库来接受和验证持有者令牌。

### <a name="install-owin"></a>安装 OWIN

首先，使用 Visual Studio 包管理器控制台安装 OWIN OAuth 身份验证管道。

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

这会安装接受和验证持有者令牌的 OWIN 中间件。

### <a name="add-an-owin-startup-class"></a>添加 OWIN 启动类

将 OWIN 启动类添加到名为 `Startup.cs` 的 API。  右键单击项目，选择“添加”和“新建项”，然后搜索“OWIN”。 当你的应用程序启动时，该 OWIN 中间件将调用 `Configuration(…)` 方法。

在本示例中，我们已将类声明更改为 `public partial class Startup`，并实现了 `App_Start\Startup.Auth.cs` 中的类的另一部分。 在 `Configuration` 方法中，我们添加了对 `Startup.Auth.cs` 中定义的 `ConfigureAuth` 的调用。 修改后，`Startup.cs` 如下所示：

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>配置 OAuth 2.0 身份验证

打开文件 `App_Start\Startup.Auth.cs` 并实现 `ConfigureAuth(...)` 方法。 例如，该方法如下所示：

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>保护任务控制器

将应用配置为使用 OAuth 2.0 身份验证后，只需将 `[Authorize]` 标记添加到任务控制器，即可保护 Web API。 所有待办事项列表操作都在此控制器中发生，因此，应在类级别保护整个控制器。 也可以将 `[Authorize]` 标记添加到单个操作，进行更细微的控制。

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>从令牌中获取用户信息

`TasksController` 将任务存储在数据库中，其中，每个任务都有一个“拥有”该任务的关联用户。 所有者按照用户的**对象 ID** 进行标识。 （这是为何需要在所有策略中添加对象 ID 作为应用程序声明的原因。）

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>验证令牌中的权限

Web API 的一个常见要求是验证令牌中存在的“作用域”。 这可确保用户已同意授予访问待办事项列表服务所需的权限。

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>运行示例应用

最后，生成并运行 `TaskWebApp` 和 `TaskService`。 在用户的待办事项列表中创建一些任务，观察是否即使停止并重新启动客户端，这些任务也仍会在 API 中保存。

## <a name="edit-your-policies"></a>编辑策略

使用 Azure AD B2C 保护 API 之后，可以试验登录/注册策略，查看它们对 API 产生的影响（也许没有影响）。 可以操作策略中的应用程序声明，然后更改 Web API 中提供的用户信息。 如本文前面所述，添加的任何声明都可供 `ClaimsPrincipal` 对象中的 .NET MVC Web API 使用。

