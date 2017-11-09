---
title: Azure Active Directory B2C | Microsoft Docs
description: "如何使用 Azure Active Directory B2C 和 OAuth 2.0 访问令牌构建 .NET Web 应用和调用 Web API。"
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 48452eb68f826d1c7aa61d5e5531f941ac1422b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C：从 .NET Web 应用调用 .NET Web API

使用 Azure AD B2C，可将强大的标识管理功能添加到 Web 应用和 Web API。 本文介绍如何请求访问令牌，以及发出从 .NET“待办事项列表”Web 应用到 .NET Web API 的调用。

本文未涵盖如何使用 Azure AD B2C 来实施登录、注册和配置文件管理， 而是着重介绍如何在用户已通过身份验证后调用 Web API。 首先应该学习以下主题：

* [.NET Web 应用](active-directory-b2c-devquickstarts-web-dotnet-susi.md)入门
* [.NET Web API](active-directory-b2c-devquickstarts-api-dotnet.md) 入门

## <a name="prerequisite"></a>先决条件

若要构建一个调用 Web API 的 Web 应用程序，需要：

1. [创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)。
2. [注册 Web API](active-directory-b2c-app-registration.md#register-a-web-api)。
3. [注册 Web 应用](active-directory-b2c-app-registration.md#register-a-web-app)。
4. [设置策略](active-directory-b2c-reference-policies.md)。
5. [授权 Web 应用使用 Web API](active-directory-b2c-access-tokens.md#publishing-permissions)。

> [!IMPORTANT]
> 客户端应用程序和 Web API 必须使用相同的 Azure AD B2C 目录。
>

## <a name="download-the-code"></a>下载代码

本教程的代码在 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) 上维护。 可运行以下命令来克隆示例：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

下载示例代码后，打开 Visual Studio .sln 文件开始处理。 解决方案文件包含两个项目：`TaskWebApp` 和 `TaskService`。 `TaskWebApp` 是用户交互的 MVC Web 应用程序。 `TaskService` 是应用的后端 Web API，存储每个用户的待办事项列表。 本文不会介绍如何构建 `TaskWebApp` Web 应用或 `TaskService` Web API。 若要了解如何使用 Azure AD B2C 构建 .NET Web 应用，请参阅 [.NET Web 应用教程](active-directory-b2c-devquickstarts-web-dotnet-susi.md)。 若要了解如何构建使用 Azure AD B2C 保护的 .NET Web API，请参阅 [.NET Web API 教程](active-directory-b2c-devquickstarts-api-dotnet.md)。

### <a name="update-the-azure-ad-b2c-configuration"></a>更新 Azure AD B2C 配置

我们的示例配置为使用演示租户的策略和客户端 ID。 如果想要使用自己的租户：

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



## <a name="requesting-and-saving-an-access-token"></a>请求和保存访问令牌

### <a name="specify-the-permissions"></a>指定权限

若要向 Web API 发出调用，需要对用户进行身份验证（使用注册/登录策略），并从 Azure AD B2C [收到访问令牌](active-directory-b2c-access-tokens.md)。 若要接收访问令牌，首先必须指定想要授予访问令牌的权限。 向 `/authorize` 终结点发出请求时，会在 `scope` 参数中指定权限。 例如，若要获取对应用 ID URI 为 `https://contoso.onmicrosoft.com/tasks` 的资源应用程序具有“读取”权限的访问令牌，范围应是 `https://contoso.onmicrosoft.com/tasks/read`。

要在本示例中指定范围，请打开文件 `App_Start\Startup.Auth.cs`，并在 OpenIdConnectAuthenticationOptions 中定义 `Scope` 变量。

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>交换访问令牌的授权代码

用户完成注册或登录体验后，应用程序将从 Azure AD B2C 接收授权代码。 OWIN OpenID Connect 中间件将存储为代码，但不会使用它来交换访问令牌。 可以使用 [MSAL 库](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)来交换。 在本示例中，我们已在 OpenID Connect 中间件中配置了一个通知回调，每当收到授权代码时，就会发出通知。 在回调中，我们使用 MSAL 交换令牌的代码并将令牌保存到缓存中。

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>调用 Web API

本部分介绍在 Azure AD B2C 中使用注册/登录期间收到的令牌来访问 Web API。

### <a name="retrieve-the-saved-token-in-the-controllers"></a>在控制器中检索保存的令牌

`TasksController` 负责与 Web API 进行通信，向 API 发送 HTTP 请求以读取、创建和删除任务。 由于 API 受 Azure AD B2C 保护，因此需要先检索上述步骤中保存的令牌。

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>从 Web API 读取任务

当拥有令牌时，可以将其附加到 `Authorization` 标头中的 HTTP `GET` 请求，以安全调用 `TaskService`：

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>在 Web API 上创建和删除任务

请遵循向 Web API 发送 `POST` 和 `DELETE` 请求时所用的相同模式，使用 MSAL 从缓存中检索访问令牌。

## <a name="run-the-sample-app"></a>运行示例应用

最后，生成并运行上述两个应用。 注册并登录，并为已登录用户创建任务。 注销并以其他用户身份登录。 为该用户创建任务。 请注意这些任务按用户存储在 API 中的方式，因为 API 从它接收的令牌中提取用户的标识。 另外，请练习使用范围。 删除“写入”权限，并尝试添加任务。 只需确保每次更改范围时注销。

