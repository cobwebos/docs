---
title: 配置用于用户登录的 Web 应用 - Microsoft 标识平台 | Azure
description: 了解如何构建用于登录用户的 Web 应用（代码配置）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b1eef510e6389b551e128877ffde723955a1084d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734631"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>可将用户登录的 Web 应用：代码配置

了解如何为可将用户登录的 Web 应用配置代码。

## <a name="libraries-for-protecting-web-apps"></a>用于保护 Web 应用的库

<!-- This section can be in an include for web app and web APIs -->
用于保护 Web 应用（和 Web API）的库为：

| 平台 | 库 | 说明 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [适用于 .NET 的标识模型扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 在由 ASP.NET 和 ASP.NET Core 直接使用的情况下，适用于 .NET 的 Microsoft 标识模型扩展提议了一组在 .NET Framework 和 .NET Core 上运行的 DLL。 在 ASP.NET 或 ASP.NET Core Web 应用中，可以使用 **TokenValidationParameters** 类控制令牌验证（尤其适用于某些合作伙伴方案）。 |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | 支持 Java Web 应用程序 |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | 支持 Python Web 应用程序 |

选择与所需平台对应的选项卡：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

本文中的代码片段及以下内容摘自 [ASP.NET Core Web 应用增量教程第 1 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)。

你可能需要参考该教程来了解完整的实现细节。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

本文中的代码片段及以下内容摘自 [ASP.NET Web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)。

你可能需要参考此示例来了解完整的实现细节。

# <a name="java"></a>[Java](#tab/java)

本文中的代码片段及以下内容摘自以 MSAL Java 编写的[调用 Microsoft Graph 的 Java Web 应用程序](https://github.com/Azure-Samples/ms-identity-java-webapp)示例。

你可能需要参考此示例来了解完整的实现细节。

# <a name="python"></a>[Python](#tab/python)

本文中的代码片段及以下内容摘自以 MSAL Python 编写的[调用 Microsoft Graph 的 Python Web 应用程序](https://github.com/Azure-Samples/ms-identity-python-webapp)示例。

你可能需要参考此示例来了解完整的实现细节。

---

## <a name="configuration-files"></a>配置文件

使用 Microsoft 标识平台将用户登录的 Web 应用程序通常是通过配置文件配置的。 需填充的设置为：

- 云实例 (`Instance`)（例如，如果需要在国家云中运行应用）
- 租户 ID 中的受众 (`TenantId`)
- 应用程序的客户端 ID (`ClientId`)，从 Azure 门户中复制

有时，可以通过 `Authority`（`Instance` 与 `TenantId` 的串联）将应用程序参数化。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，这些设置位于 [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) 文件的“AzureAd”节中。

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
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

在 ASP.NET Core 中，另一个文件 ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) 包含应用程序的 URL (`applicationUrl`) 和 TLS/SSL 端口 (`sslPort`)，以及各种配置文件。

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

在 Azure 门户中，需要在应用程序的“身份验证”页中注册的回复 URI 需与这些 URL 相匹配。  对于上述两个配置文件，URL 为 `https://localhost:44321/signin-oidc`。 原因是 `applicationUrl` 为 `http://localhost:3110`，但指定的是 `sslPort` (44321)。 `CallbackPath` 是 `appsettings.json` 中定义的 `/signin-oidc`。

注销 URI 将采用相同方式设置为 `https://localhost:44321/signout-callback-oidc`。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，应用程序通过 [Web.Config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) 文件的第 12 到 15 行进行配置。

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

在 Azure 门户中，需要在应用程序的“身份验证”页中注册的回复 URI 需与这些 URL 相匹配。  即，它们应是 `https://localhost:44326/`。

# <a name="java"></a>[Java](#tab/java)

在 Java 中，配置位于 [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) 文件中的 `src/main/resources` 下。

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

在 Azure 门户中，需要在应用程序的“身份验证”页中注册的回复 URI 需与应用程序定义的 `redirectUri` 实例相匹配。  即，它们应是 `http://localhost:8080/msal4jsample/secure/aad` 和 `http://localhost:8080/msal4jsample/graph/me`。

# <a name="python"></a>[Python](#tab/python)

下面是 [app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py) 中的 Python 配置文件：

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> 本快速入门建议在配置文件中存储客户端机密，以方便操作。 在生产应用中，建议使用其他方式来存储机密，例如，使用 Key Vault，或者根据 [Flask 文档](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)中所述使用环境变量。
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>初始化代码

初始化代码因平台而异。 对于 ASP.NET Core 和 ASP.NET，用户登录将委托给 OpenID Connect 中间件来完成。 ASP.NET 或 ASP.NET Core 模板可为 Azure Active Directory (Azure AD) v1.0 终结点生成 Web 应用程序。 只需进行一些配置就能使这些应用程序适应 Microsoft 标识平台 (v2.0) 终结点。 对于 Java，初始化由 Spring 在应用程序的配合下进行处理。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core Web 应用（和 Web API）中，应用程序受到保护，因为控制器或控制器操作中包含 `[Authorize]` 属性。 此属性检查是否已对用户进行身份验证。 正在初始化该应用程序的代码位于*Startup.cs*文件中。

若要使用 Microsoft 标识平台（前称为 Azure AD v2.0）添加身份验证，需要添加以下代码。 代码中的注释应该浅显易懂。

> [!NOTE]
> 如果使用 Visual Studio 中的默认 ASP.NET Core web 项目或通过使用`dotnet new mvc --auth SingleAuth`或`dotnet new webapp --auth SingleAuth`来启动项目，则会看到如下所示的代码： `services.AddAuthentication(AzureADDefaults.AuthenticationScheme).AddAzureAD(options => Configuration.Bind("AzureAd", options));`。
> 
> 此代码使用旧的**AspNetCore AzureAD** NuGet 包，用于创建 Azure AD v1.0 的应用程序。 本文介绍如何创建一个替换该代码的 Microsoft 标识平台（Azure AD v2.0）应用程序。

1. 向你的项目添加 " [microsoft. web](https://www.nuget.org/packages/Microsoft.Identity.Web) " 和 " [microsoft](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) " NuGet 包。 如果存在 AspNetCore，请删除 AzureAD NuGet 包。

2. 更新中`ConfigureServices`的代码，使它使用`AddSignIn`和`AddMicrosoftIdentityUI`方法。

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddSignIn(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. 在 Startup.cs `Configure`的方法*Startup.cs*中，通过调用启用身份验证`app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

在上面的代码中：
- 扩展`AddSignIn`方法是在 " **web.config**" 中定义的。 该方法：
  - 添加身份验证服务。
  - 配置用于读取配置文件的选项（此处为 "AzureAD" 部分）
  - 配置 OpenID Connect 选项，使该颁发机构成为 Microsoft 标识平台终结点。
  - 验证令牌的颁发者。
  - 确保从 ID 令牌中的 `preferred_username` 声明映射与名称对应的声明。

- 除了配置对象之外，还可以在调用`AddSignIn`时指定配置节的名称。 该名称默认为 `AzureAd`。

- `AddSignIn`具有高级方案的其他参数。 例如，如果身份验证不起作用，则跟踪 OpenID Connect 中间件事件可帮助你对 web 应用程序进行故障排除。 将可选参数`subscribeToOpenIdConnectMiddlewareDiagnosticsEvents`设置为`true`时，将显示一组 ASP.NET Core 中间件的处理方式，因为它从 HTTP 响应到中`HttpContext.User`用户的标识进行处理。

- 扩展`AddMicrosoftIdentityUI`方法是在 " **web.config**" 中定义的。 它提供了一个用于处理注销的默认控制器。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

与 ASP.NET Web 应用和 Web API 中的身份验证相关的代码位于 [App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) 文件中。

```csharp
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

# <a name="java"></a>[Java](#tab/java)

该 Java 示例使用 Spring 框架。 应用程序受到保护，因为已实现一个用于截获每个 HTTP 响应的筛选器。 在 Java Web 应用快速入门中，此筛选器是 `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` 中的 `AuthFilter`。

该筛选器处理 OAuth 2.0 授权代码流，并检查用户是否已完成身份验证（`isAuthenticated()` 方法）。 如果用户未完成身份验证，该筛选器将计算 Azure AD 授权终结点的 URL，并将浏览器重定向到此 URI。

包含授权代码的响应抵达时，它将使用 MSAL Java 获取令牌。 当它最终从令牌终结点收到令牌时（在重定向 URI 上），用户已登录。

有关详细信息，请参阅 [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java) 中的 `doFilter()` 方法。

> [!NOTE]
> `doFilter()` 的代码以略微不同的顺序编写，但流与上述相同。

有关此方法触发的授权代码流的详细信息，请参阅 [Microsoft 标识平台和 OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

# <a name="python"></a>[Python](#tab/python)

该 Python 示例使用 Flask。 Flask 和 MSAL.Python 的初始化在 [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28) 中完成。

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

下一篇文章将会介绍如何触发登录和注销。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [登录和注销](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [登录和注销](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [登录和注销](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [登录和注销](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
