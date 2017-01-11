---
title: Azure AD B2C | Microsoft Docs
description: "如何使用 Azure Active Directory B2C 构建 .NET Web API，以及使用 OAuth 2.0 身份验证访问令牌保护该 API。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1062af9abfd167dd251621a43943dea399aed027


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C：构建 .NET Web API
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

借助 Azure Active Directory (Azure AD) B2C，可以使用 OAuth 2.0 访问令牌来保护 Web API。 这些令牌允许使用 Azure AD B2C 的客户端应用对 API 进行身份验证。 本文说明如何创建一个 .NET 模型-视图-控制器 (MVC)“待办事项列表”API，使用户能够执行 CRUD 任务。 Web API 使用 Azure AD B2C 进行保护，只允许经过身份验证的用户管理其待办事项列表。

## <a name="create-an-azure-ad-b2c-directory"></a>创建 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后继续执行本指南中的步骤。

## <a name="create-an-application"></a>创建应用程序
接下来，需要在 B2C 目录中创建应用。 此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 若要创建应用，请遵循 [这些说明](active-directory-b2c-app-registration.md)。 请务必：

* 在应用程序中包含一个 **Web 应用**或 **Web API**。
* 对 Web 应用使用**重定向统一资源标识符** `https://localhost:44316/`。 这是用于本代码示例的 Web 应用客户端的默认位置。
* 复制分配给应用的 **应用程序 ID** 。 稍后需要用到此信息。

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略
在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 本代码示例中的客户端包含三个标识体验：注册、登录和编辑配置文件。 需要按照 [策略参考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)中所述，为每个类型创建一个策略。 创建三个策略时，请务必：

* 在标识提供者边栏选项卡中，选择“用户 ID 注册”或“电子邮件注册”。
* 在注册策略中，选择“显示名称”和其他注册属性。
* 针对每个策略选择“显示名称”和“对象 ID”声明作为应用程序声明。 也可以选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 稍后需要用到这些策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

成功创建三个策略后，可以开始构建应用。

## <a name="download-the-code"></a>下载代码
本教程的代码 [保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet)。 若要根据说明构建示例，请 [下载 .zip 文件格式的骨干项目](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip)。 也可以克隆骨干项目：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

完成的应用也[作为 .zip 文件提供](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip)，或者放在同一存储库的 `complete` 分支中。

下载示例代码后，打开 Visual Studio .sln 文件开始处理。 解决方案文件包含两个项目：`TaskWebApp` 和 `TaskService`。 `TaskWebApp` 是用户交互的 MVC Web 应用程序。 `TaskService` 是应用的后端 Web API，存储每个用户的待办事项列表。

## <a name="configure-the-task-web-app"></a>配置任务 Web 应用
用户与 `TaskWebApp` 交互时，客户端将请求发送给 Azure AD，然后取回可用于调用 `TaskService` Web API 的令牌。 若要使用户登录并获取令牌，必须向 `TaskWebApp` 提供有关应用的某些信息。 在 `TaskWebApp` 项目中，打开位于项目根目录中的 `web.config` 文件，并替换 `<appSettings>` 节中的值。  `AadInstance`、`RedirectUri` 和 `TaskServiceUrl` 值可保留原样。

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

本文不会介绍如何构建 `TaskWebApp` 客户端。  若要了解如何使用 Azure AD B2C 构建 Web 应用，请参阅 [.NET Web 应用教程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="secure-the-api"></a>保护 API
如果某个客户端可以代表用户调用 API，可以使用 OAuth 2.0 持有者令牌来保护 `TaskService` 。 API 可以使用适用于 .NET 的开放式 Web 接口 (OWIN) 库来接受和验证令牌。

### <a name="install-owin"></a>安装 OWIN
首先，请安装 OWIN OAuth 身份验证管道：

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>输入 B2C 详细信息
打开位于 `TaskService` 项目根目录中的 `web.config` 文件，并替换 `<appSettings>` 节中的值。 这些值将在整个 API 和 OWIN 库中使用。  可将 `AadInstance` 值保持不变。

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>添加 OWIN 启动类
将名为 `Startup.cs` 的 OWIN 启动类添加到 `TaskService` 项目。  右键单击项目，选择“添加”和“新建项”，然后搜索“OWIN”。

```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>配置 OAuth 2.0 身份验证
打开文件 `App_Start\Startup.Auth.cs` 并实现 `ConfigureAuth(...)` 方法：

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>保护任务控制器
将应用配置为使用 OAuth 2.0 身份验证后，只需将 `[Authorize]` 标记添加到任务控制器，即可保护 Web API。 所有待办事项列表操作都在此控制器中发生，因此，应在类级别保护整个控制器。 也可以将 `[Authorize]` 标记添加到单个操作，进行更细微的控制。

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>从令牌中获取用户信息
`TasksController` 将任务存储在数据库中，其中，每个任务都有一个“拥有”该任务的关联用户。 所有者按照用户的**对象 ID** 进行标识。 （这是为何需要在所有策略中添加对象 ID 作为应用程序声明的原因。）

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>运行示例应用
最后，生成并运行 `TaskWebApp` 和 `TaskService`。 使用电子邮件地址或用户名注册应用。 在用户的待办事项列表中创建一些任务，观察是否即使停止并重新启动客户端，这些任务也仍会在 API 中保存。

## <a name="edit-your-policies"></a>编辑策略
使用 Azure AD B2C 保护 API 之后，可以在应用中尝试运行策略，查看它们对 API 产生的影响（也许没有影响）。 可以操作策略中的应用程序声明，然后更改 Web API 中提供的用户信息。 如本文前面所述，添加的任何声明都可供 `ClaimsPrincipal` 对象中的 .NET MVC Web API 使用。

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Dec16_HO4-->


