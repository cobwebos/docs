---
title: 配置登录用户的 web 应用-Microsoft 标识平台 |Microsoft
description: 了解如何生成用于登录用户的 web 应用（代码配置）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b077a71a541d29c9b93778babc096ea40c3b43cb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964865"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>登录用户的 Web 应用：代码配置

了解如何为 web 应用配置登录用户的代码。

## <a name="libraries-for-protecting-web-apps"></a>用于保护 web 应用的库

<!-- This section can be in an include for Web App and Web APIs -->
用于保护 web 应用（和 web API）的库包括：

| 平台 | 库 | 描述 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [适用于 .NET 的标识模型扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 适用于 .NET Framework .NET 的 Microsoft 标识模型扩展，可由 ASP.NET 和 ASP.NET Core 直接使用。 从 ASP.NET 或 ASP.NET Core web 应用，可以使用**TokenValidationParameters**类（特别是在某些合作伙伴方案中）控制令牌验证。 |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | 适用于 Java 的 Microsoft 身份验证库（MSAL）。 当前在公共预览版中。 |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | 用于 Python 的 MSAL。 当前在公共预览版中。 |

选择与你感兴趣的平台相对应的选项卡：

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

本文中的代码片段和以下文章摘自[ASP.NET Core web 应用增量教程第1章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)。

有关完整的实现的详细信息，请参阅本教程。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

本文中的代码片段和以下文章摘自[ASP.NET web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)。

你可能想要参考此示例以获取完整的实现细节。

# <a name="javatabjava"></a>[Java](#tab/java)

本文中的代码片段和下面的代码片段从 MSAL Java 中[调用 Microsoft graph 示例的 java web 应用程序](https://github.com/Azure-Samples/ms-identity-java-webapp)中提取。

你可能想要参考此示例以获取完整的实现细节。

# <a name="pythontabpython"></a>[Python](#tab/python)

本文中的代码片段和以下代码片段从 MSAL Python 中[调用 Microsoft graph 示例的 Python web 应用程序](https://github.com/Azure-Samples/ms-identity-python-webapp)中提取。

你可能想要参考此示例以获取完整的实现细节。

---

## <a name="configuration-files"></a>配置文件

使用 Microsoft 标识平台登录用户的 Web 应用程序通常是通过配置文件配置的。 需要填写的设置如下：

- 如果希望应用在全国云中运行，则为云实例（`Instance`），例如
- 租户 ID 中的受众（`TenantId`）
- 从 Azure 门户中复制的应用程序的客户端 ID （`ClientId`）

有时，应用程序可以由 `Authority`参数化，这是 `Instance` 和 `TenantId`的串联。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，这些设置位于 "AzureAd" 节中的[appsettings](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8)文件中。

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

在 ASP.NET Core 中，另一个文件（[properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)）包含应用程序的 URL （`applicationUrl`）以及 SSL 端口（`sslPort`）和不同的配置文件。

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

在 Azure 门户中，需要在应用程序的**身份验证**页上注册的答复 uri 需要匹配这些 url。 对于上述两个配置文件，这些文件将被 `https://localhost:44321/signin-oidc`。 原因是 `applicationUrl` `http://localhost:3110`，但指定了 `sslPort` （44321）。 `CallbackPath` 是 `/signin-oidc`的，如 `appsettings.json`中所定义。

同样，注销 URI 也将设置为 `https://localhost:44321/signout-callback-oidc`。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，应用程序通过[web.config 文件进行](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15)配置，第12行到第15行。

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

在 Azure 门户中，需要在应用程序的**身份验证**页上注册的答复 uri 需要匹配这些 url。 也就是说，它们应该 `https://localhost:44326/`。

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 中，配置位于位于 `src/main/resources`下的[application. properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties)文件中。

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

在 Azure 门户中，需要在应用程序的**身份验证**页上注册的答复 uri 需要与应用程序定义的 `redirectUri` 实例相匹配。 也就是说，应 `http://localhost:8080/msal4jsample/secure/aad` 和 `http://localhost:8080/msal4jsample/graph/me`。

# <a name="pythontabpython"></a>[Python](#tab/python)

下面是 app_config 中的 Python 配置文件[。 py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)：

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> 本快速入门建议在配置文件中存储客户端机密，以便简单起见。 在生产应用中，需要使用其他方法来存储机密，如[Flask 的文档](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)中所述的密钥保管库或环境变量。
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>初始化代码

初始化代码因平台而异。 对于 ASP.NET Core 和 ASP.NET，将用户登录到 OpenID Connect 中间件。 ASP.NET 或 ASP.NET Core 模板为 Azure Active Directory （Azure AD） v1.0 终结点生成 web 应用程序。 需要进行一些配置才能适应 Microsoft 标识平台（v2.0）终结点。 对于 Java，它是通过与应用程序的协作进行弹簧处理的。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core web 应用（和 web Api）中，应用程序受到保护，因为控制器或控制器操作具有 `[Authorize]` 属性。 此属性检查是否对用户进行了身份验证。 正在初始化该应用程序的代码位于 Startup.cs 文件中。 

若要通过 Microsoft 标识平台（以前称为 Azure AD v2.0）添加身份验证，则需要添加以下代码。 代码中的注释应一目了然。

> [!NOTE]
> 如果使用 Visual studio 中的默认 ASP.NET Core web 项目或通过使用 `dotnet new mvc`启动项目，则默认情况下会提供 `AddAzureAD` 方法。 这是因为会自动加载相关的包。
>
> 如果你从头开始生成一个项目并尝试使用以下代码，我们建议你将 NuGet 包**AspNetCore**添加到你的项目，以使 `AddAzureAD` 方法可用。

可从[启动 .cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)获取以下代码。

```CSharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

`AddMicrosoftIdentityPlatformAuthentication` 扩展方法是在 WebAppServiceCollectionExtensions [/L23 #](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)中定义的。 以便

- 添加身份验证服务。
- 配置用于读取配置文件的选项。
- 配置 OpenID Connect 选项，使所用的颁发机构为 Microsoft 标识平台（以前称为 Azure AD v2.0）终结点。
- 验证令牌的颁发者。
- 确保从 ID 令牌中的 `preferred_username` 声明中映射与名称对应的声明。

除了配置外，还可以在调用 `AddMicrosoftIdentityPlatformAuthentication`时指定配置节的名称。 默认情况下，它 `AzureAd`。

如果身份验证不起作用，则跟踪 OpenId Connect 中间件事件可帮助你对 web 应用程序进行故障排除。 将 `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` 设置为 `true` 将向你显示在 ASP.NET Core 中间件从 HTTP 响应前进到 `HttpContext.User`中的用户标识时，如何详细阐述信息。

```CSharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

在许多情况下，`AadIssuerValidator` 类允许验证令牌的颁发者。 此类适用于1.0 版或 v2.0 版令牌、单租户或多租户应用程序，或在 Azure 公有云和全国云中使用个人 Microsoft 帐户登录用户的应用程序。 它可通过[AadIssuerValidator/资源/cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)获得。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

与 ASP.NET web 应用和 web Api 中的身份验证相关的代码位于[App_Start/startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)文件中。

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java 示例使用弹簧框架。 应用程序受到保护，因为你实现了一个筛选器，该筛选器会截获每个 HTTP 响应。 在 Java web 应用快速入门中，此筛选器在 `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`中 `AuthFilter`。 

筛选器处理 OAuth 2.0 授权代码流，并检查用户是否已通过身份验证（`isAuthenticated()` 方法）。 如果用户未通过身份验证，则会计算 Azure AD 授权终结点的 URL，并将浏览器重定向到此 URI。

当响应到达（包含授权代码）时，它将使用 MSAL Java 获取令牌。 最终从令牌终结点接收令牌时（在重定向 URI 上），用户已登录。

有关详细信息，请参阅[AuthFilter](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)中的 `doFilter()` 方法。

> [!NOTE]
> 以略微不同的顺序编写 `doFilter()` 的代码，但流是所述的。

有关此方法触发的授权代码流的详细信息，请参阅[Microsoft 标识平台和 OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 示例使用 Flask。 Flask 和 MSAL Python 的初始化是在[py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)中完成的。

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>后续步骤

在下一篇文章中，你将学习如何触发登录和注销。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [登录和注销](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [登录和注销](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [登录和注销](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [登录和注销](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
