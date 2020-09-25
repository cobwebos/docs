---
title: 配置受保护的 Web API 应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何生成受保护的 Web API 和配置应用程序的代码。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 613ba527c8f86257dd271d3cc9e43c97fc475068
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257446"
---
# <a name="protected-web-api-code-configuration"></a>受保护的 Web API：代码配置

若要配置受保护 Web API 的代码，需要了解：

- API 受保护的定义是什么。
- 如何配置持有者令牌。
- 如何验证令牌。

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>ASP.NET 和 ASP.NET Core API 受保护是什么意思？

与 Web 应用一样，ASP.NET 和 ASP.NET Core Web API 也受到保护，因为它们的控制器操作以 **[Authorize]** 属性为前缀。 仅当使用已授权的标识调用 API 时，才能调用控制器操作。

考虑以下问题：

- 只有应用可以调用 Web API。 API 如何识别调用它的应用的标识？
- 如果应用代表某个用户调用 API，该用户的标识是什么？

## <a name="bearer-token"></a>持有者令牌

调用应用时在标头中设置的持有者令牌包含有关应用标识的信息。 除非 Web 应用接受来自守护程序应用的服务到服务调用，否则该令牌还包含有关用户的信息。

以下 C# 代码示例演示了某个客户端在使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 获取令牌后调用 API：

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> 客户端应用程序向 Web API 的 Microsoft 标识平台终结点请求持有者令牌。  Web API 是唯一应该验证令牌并查看其中包含的声明的应用程序。 客户端应用永远不会尝试检查令牌中的声明。
>
> 将来，Web API 可能要求加密令牌。 这项要求会阻止可以查看访问令牌的客户端应用进行访问。

## <a name="jwtbearer-configuration"></a>JwtBearer 配置

本部分介绍如何配置持有者令牌。

### <a name="config-file"></a>配置文件

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>为 Web API 使用自定义应用 ID URI 时

如果已接受应用注册门户建议的应用 ID URI，则无需指定访问群体 (参阅 [应用程序 ID uri 和范围](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)) 。 否则，应添加一个 `Audience` 属性，其值为 Web API 的应用 ID URI。

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>代码初始化

对包含 **[Authorize]** 属性的控制器操作调用某个应用时，ASP.NET 和 ASP.NET Core 将从 Authorization 标头的持有者令牌中提取访问令牌。 然后，该访问令牌将转发到 JwtBearer 中间件，而该中间件会调用适用于 .NET 的 Microsoft IdentityModel 扩展。

#### <a name="microsoftidentityweb"></a>Web.config

Microsoft 建议你在使用 ASP.NET Core 开发 web API 时使用 [Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Web) web.config NuGet 包。

_Microsoft_ 在 ASP.NET Core、身份验证中间件以及用于 .NET [ (MSAL) 的 Microsoft 身份验证库 ](msal-overview.md) 之间提供粘附。 它允许更清晰、更可靠的开发人员体验，并利用 Microsoft 标识平台和 Azure AD B2C 的强大功能。

#### <a name="using-microsoftidentityweb-templates"></a>使用 Microsoft.Identity.Web 模板

可以通过使用 Microsoft.Identity.Web 项目模板从头开始创建一个 Web API。 有关详细信息，请参阅 [Microsoft.Identity.Web - Web API 项目模板](https://aka.ms/ms-id-web/webapi-project-templates)

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>从现有 ASP.NET Core 3.1 应用程序开始

目前，ASP.NET Core 3.1 使用 Microsoft.AspNetCore.AzureAD.UI 库。 该中间件在 Startup.cs 文件中初始化。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

该中间件由以下指令添加到 Web API：

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 目前，ASP.NET Core 模板会创建可将你的组织或任何组织中的用户登录的 Azure Active Directory (Azure AD) Web API。 他们不会通过个人帐户登录用户。 但是，你可以通过使用*Startup.cs*替换以下代码，将模板更改为使用[microsoft 标识](https://www.nuget.org/packages/Microsoft.Identity.Web)平台终结点：

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

你也可以编写以下内容（这是等效的）

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> 如果使用 Microsoft.Identity.Web，但未在 appsettings.json 中设置 `Audience`，则使用以下内容：
> -  如果已将[接受访问令牌的版本](scenario-protected-web-api-app-registration.md#accepted-token-version)设置为 `2` 或用于 Azure AD B2C Web API，则使用 `$"{ClientId}"`。
> - 在所有其他情况下（用于 v1.0 [访问令牌](access-tokens.md)），使用 `$"api://{ClientId}`。
> 有关详细信息，请参阅 Microsoft.Identity.Web [源代码](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83)。

前面的代码段摘自 [ASP.NET Core Web API 增量教程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28)。 [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiServiceCollectionExtensions.cs#L27) 中提供了 AddMicrosoftIdentityWebApiAuthentication 的详细信息。 此方法调用 [AddMicrosoftWebAPI](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiAuthenticationBuilderExtensions.cs#L58)，后者会指示中间件如何验证令牌。 有关详细信息，请参阅其[源代码](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiAuthenticationBuilderExtensions.cs#L104-L122)。

## <a name="token-validation"></a>令牌验证

在上述代码片段中，与 Web 应用中的 OpenID Connect 中间件一样，JwtBearer 中间件将会根据 `TokenValidationParameters` 的值验证令牌。 根据需要解密该令牌，提取声明，并验证签名。 然后，该中间件将通过检查以下数据来验证该令牌：

- 受众：令牌面向 Web API。
- 使用者：令牌是针对可以调用 Web API 的应用颁发的。
- 颁发者：令牌是由受信任的安全令牌服务 (STS) 颁发的。
- 过期时间：令牌的生存期在有效范围内。
- 签名：令牌未被篡改。

此外还可以执行特殊验证。 例如，可以验证嵌入在令牌中的签名密钥是否受信任，以及令牌是否未重放。 最后，某些协议需要特定的验证。

### <a name="validators"></a>验证程序

在[适用于 .NET 的 Microsoft 标识模型扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)开源库提供的验证程序中捕获验证步骤。 验证程序在库源文件 [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs) 中定义。

下表描述了验证程序：

| 验证程序 | 说明 |
|---------|---------|
| **ValidateAudience** | 确保该令牌适用于为你验证令牌的应用程序。 |
| **ValidateIssuer** | 确保令牌由受信任的 STS 颁发，即，令牌来自你信任的某人。 |
| **ValidateIssuerSigningKey** | 确保验证令牌的应用程序信任用于签署令牌的密钥。 在令牌中嵌入密钥存在一种特殊情况。 但这种情况通常不会出现。 |
| **ValidateLifetime** | 确保令牌仍然或已经生效。 验证程序将检查令牌的生存期是否在 **notbefore** 和 **expires** 声明指定的范围内。 |
| **ValidateSignature** | 确保令牌未被篡改。 |
| **ValidateTokenReplay** | 确保令牌未重放。 某些一次性协议存在一种特殊情况。 |

#### <a name="customizing-token-validation"></a>自定义令牌验证

验证程序与 **TokenValidationParameters** 类的属性相关联。 这些属性从 ASP.NET 和 ASP.NET Core 配置初始化。

在大多数情况下，无需更改参数， 非单租户应用除外。 这些 web 应用将接受来自任何组织或个人 Microsoft 帐户的用户。 对于这种情况，必须验证颁发者。 Microsoft.Identity.Web 还负责颁发者验证。 有关详细信息，请参阅 Microsoft.Identity.Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)。

在 ASP.NET Core 中，如果要自定义令牌验证参数，请在 Startup.cs 中使用以下代码片段：

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

对于 ASP.NET MVC，下面的代码示例演示了如何执行自定义令牌验证：

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Azure Functions 中的令牌验证

还可以在 Azure Functions 中验证传入的访问令牌。 可以在 GitHub 上的以下代码示例中找到此类验证的示例：

- .NET:[Azure-Samples/ms-identity-dotnet-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js：[Azure-Samples/ms-identity-nodejs-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python:[Azure-Samples/ms-identity-python-webapi-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [验证代码中的范围和应用角色](scenario-protected-web-api-verification-scope-app-roles.md)
