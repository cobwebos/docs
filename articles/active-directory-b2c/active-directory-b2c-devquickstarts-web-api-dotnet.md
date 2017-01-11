---
title: Azure Active Directory B2C | Microsoft Docs
description: "如何生成使用 Azure Active Directory B2C 调用 Web API 的 Web 应用程序。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: b235c7a773b1c7c2a5f4d5825c6a20c1b0afb7fb


---
# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure AD B2C：从 .NET Web 应用调用 Web API
通过使用 Azure Active Directory (Azure AD) B2C，只需几个简短的步骤即可将强大的自助服务标识管理功能添加到 Web 应用和 Web API。 本文将讨论如何创建一个通过使用持有者令牌调用 Web API 的 .NET 模型-视图-控制器 (MVC)“待办事项列表”Web 应用

本文未涵盖如何使用 Azure AD B2C 来实施登录、注册和配置文件管理， 而是着重介绍如何在用户已通过身份验证后调用 Web API。 如果尚未准备好，请先阅读 [.NET Web 应用快速入门教程](active-directory-b2c-devquickstarts-web-dotnet.md)，了解 Azure AD B2C 的基本知识。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。  目录是所有用户、应用、组等对象的容器。  如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后继续执行本指南中的步骤。

## <a name="create-an-application"></a>创建应用程序
接下来，需要在 B2C 目录中创建应用。 此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 在本例中，由于 Web 应用与 Web API 构成一个逻辑应用，因此将由单个 **应用程序 ID**表示。 若要创建应用，请遵循 [这些说明](active-directory-b2c-app-registration.md)。 请务必：

* 在应用程序中包含 **Web 应用/Web API**。
* 在“回复 URL”中输入 `https://localhost:44316/`。 它是此代码示例的默认 URL。
* 复制分配给应用的 **应用程序 ID** 。 稍后也需要用到此信息。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略
在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 此 Web 应用包含三个标识体验：注册、登录和编辑配置文件。 需要按 [策略参考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)中所述，为每个类型创建一个策略。 创建三个策略时，请务必：

* 在注册策略中，选择“显示名称”和其他注册属性。
* 在每个策略中，选择“显示名称”和“对象 ID”应用程序声明。 也可以选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 其前缀应为 `b2c_1_`。 稍后将需要这些策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

创建三个策略后，可以开始构建应用。

请注意，本文未介绍如何使用刚刚创建的策略。 若要了解 Azure AD B2C 中策略的工作原理，请先阅读 [.NET Web 应用入门教程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="download-the-code"></a>下载代码
本教程的代码 [保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet)。 若要根据说明构建示例，可以[下载 .zip 文件格式的骨干项目](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip)。 也可以克隆骨干项目：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

完成的应用也[作为 .zip 文件提供](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)，或者放在同一存储库的 `complete` 分支中。

下载示例代码后，打开 Visual Studio .sln 文件开始处理。

## <a name="configure-the-task-web-app"></a>配置任务 Web 应用
若要使 `TaskWebApp` 与 Azure AD B2C 进行通信，需要提供几个常见参数。 在 `TaskWebApp` 项目中，打开位于项目根目录中的 `web.config` 文件，并替换 `<appSettings>` 节中的值。 `AadInstance`、`RedirectUri` 和 `TaskServiceUrl` 值可保留原样。

```
  <appSettings>

    ...

    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>获取访问令牌并调用任务 API
本节将讨论如何使用 Azure AD B2C 登录期间接收的令牌，访问同样受 Azure AD B2C 保护的 Web API。

本文未介绍如何有关保护 API 的详细信息。 若要了解 Web API 如何使用 Azure AD B2C 安全验证请求，请查看 [Web API 快速入门文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

### <a name="save-the-sign-in-token"></a>保存登录令牌
首先，使用任一策略验证用户，并从 Azure AD B2C 接收登录令牌。  如果不确定该如何执行策略，请返回并阅读 [.NET Web 应用快速入门教程](active-directory-b2c-devquickstarts-web-dotnet.md)，了解 Azure AD B2C 的基本知识。

打开 `App_Start\Startup.Auth.cs` 文件。  必须对 `OpenIdConnectAuthenticationOptions` 进行一项重要更改，即设置 `SaveSignInToken = true`。

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",

        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>在控制器中获取令牌
`TasksController` 负责与 Web API 进行通信，向 API 发送 HTTP 请求以读取、创建和删除任务。  由于 API 受 Azure AD B2C 保护，因此需要先检索上述步骤中保存的令牌。

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;

    ...
}
```

`BootstrapContext` 包含通过执行其中一个 B2C 策略所获取的登录令牌。

### <a name="read-tasks-from-the-web-api"></a>从 Web API 读取任务
当拥有令牌时，可以将其附加到 `Authorization` 标头中的 HTTP `GET` 请求，以安全调用 `TaskService`：

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>在 Web API 上创建和删除任务
请遵循与向 Web API 发送 `POST` 和 `DELETE` 请求时相同的模式，使用 `BootstrapContext` 检索登录令牌。 我们为你实现了创建操作。 你可以尝试在 `TasksController.cs` 中完成删除操作。

## <a name="run-the-sample-app"></a>运行示例应用
最后，生成并运行应用。 注册并登录，并为已登录用户创建任务。 注销并以其他用户身份登录。 为该用户创建任务。 请注意这些任务按用户存储在 API 中的方式，因为 API 从它接收的令牌中提取用户的标识。

为方便参考，已完成的示例[以 .zip 文件形式提供](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)。 也可以从 GitHub 克隆它：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->



<!--HONumber=Dec16_HO4-->


