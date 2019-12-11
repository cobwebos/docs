---
title: 配置受保护的 web API 应用 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建受保护的 web API 并配置你的应用程序代码。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f78fa35096b7e17d3736190bfa49619c2c81520
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965392"
---
# <a name="protected-web-api-code-configuration"></a>受保护的 web API：代码配置

若要为受保护的 web API 配置代码，你需要了解哪些内容将 Api 定义为受保护，如何配置持有者令牌，以及如何验证令牌。

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>什么定义受保护的 ASP.NET/ASP.NET 核心 Api？

与 web 应用一样，ASP.NET/ASP.NET Core web Api 是 "受保护的"，因为其控制器操作以 `[Authorize]` 属性为前缀。 因此，仅当使用授权的身份调用 API 时，才能调用控制器操作。

请考虑下列问题：

- Web API 如何识别调用它的应用的标识？ （仅应用可以调用 web API。）
- 如果应用代表用户调用 web API，用户的标识是什么？

## <a name="bearer-token"></a>持有者令牌

有关应用标识和用户的信息（除非 web 应用接受来自后台应用的服务到服务调用）保存在调用应用时在标头中设置的持有者令牌中。

下面是一个C#代码示例，该示例演示了在使用适用于 .Net 的 Microsoft 身份验证库（MSAL.NET）获取令牌后调用 API 的客户端：

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> 客户端应用程序向*WEB API*的 Microsoft 标识平台终结点请求持有者令牌。 Web API 是唯一应该验证令牌并查看其包含的声明的应用程序。 客户端应用程序不应尝试检查令牌中的声明。 （将来，web API 可能需要加密令牌。 此要求会阻止对可以查看访问令牌的客户端应用的访问。）

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

### <a name="code-initialization"></a>代码初始化

当在承载 `[Authorize]` 特性的控制器操作上调用应用程序时，ASP.NET/ASP.NET Core 将在调用请求的 Authorization 标头中查看持有者令牌并提取访问令牌。 然后，将令牌转发到 JwtBearer 中间件，该中间件会调用 Microsoft System.identitymodel Extensions for .NET。

在 ASP.NET Core 中，将在 Startup.cs 文件中初始化该中间件：

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

此指令将中间件添加到 web API：

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 目前，ASP.NET Core 模板创建了 Azure Active Directory （Azure AD） web Api，这些 Api 在组织或任何组织中登录用户，而不是通过个人帐户登录。 但你可以通过将以下代码添加到 Startup.cs 文件，轻松地将其更改为使用 Microsoft 标识平台终结点：

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

此代码段摘自 WebApiServiceCollectionExtensions [/# L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)中的 ASP.NET Core Web Api 增量教程。 `AddProtectedWebApi` 方法，该方法执行更多的调用，从 Startup.cs

## <a name="token-validation"></a>令牌验证

JwtBearer 中间件（如 web apps 中的 OpenID Connect 中间件）由 `TokenValidationParameters` 定向来验证令牌。 令牌已解密（根据需要），提取声明，并验证签名。 然后，中间件通过检查此数据来验证令牌：

- 它的目标是 web API （受众）。
- 它是为允许调用 web API （sub）的应用程序颁发的。
- 它是由受信任的 security token service （STS）（颁发者）颁发的。
- 其生存期处于范围（到期）中。
- 它未被篡改（签名）。

还可以进行特殊验证。 例如，可以验证签名密钥（嵌入到令牌中时）是否受信任，以及令牌是否未被重播。 最后，某些协议需要进行特定验证。

### <a name="validators"></a>验证程序

验证步骤是在验证器中捕获的，它们全都位于 .NET 开源库的[Microsoft System.identitymodel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)中，位于一个源文件中： [system.identitymodel/验证器。](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

此表描述了验证程序：

| 基值 | 描述 |
|---------|---------|
| `ValidateAudience` | 确保令牌适用于验证令牌的应用程序（适用于我）。 |
| `ValidateIssuer` | 确保令牌由受信任的 STS （来自我信任的人）颁发。 |
| `ValidateIssuerSigningKey` | 确保验证令牌的应用程序信任用于对令牌进行签名的密钥。 （密钥嵌入令牌中的特例。 通常不需要。） |
| `ValidateLifetime` | 确保标记仍然有效。 验证程序将检查令牌的生存期（`notbefore` 和 `expires` 声明）是否在范围内。 |
| `ValidateSignature` | 确保标记未被篡改。 |
| `ValidateTokenReplay` | 确保标记不会重播。 （某些一次性使用协议的特殊情况。） |

验证程序均与 `TokenValidationParameters` 类的属性关联，这些属性本身是从 ASP.NET/ASP.NET 核心配置中初始化的。 在大多数情况下，无需更改参数。 对于不是单租户的应用程序，有一个例外情况。 （即，接受来自任何组织或个人 Microsoft 帐户的用户的 web 应用。）在这种情况下，必须验证颁发者。

## <a name="token-validation-in-azure-functions"></a>Azure Functions 中的令牌验证

还可以在 Azure 函数中验证传入的访问令牌。 可以在 Azure 函数的[Dotnet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)、 [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)和[Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)中找到验证令牌的示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [验证代码中的作用域和应用程序角色](scenario-protected-web-api-verification-scope-app-roles.md)
