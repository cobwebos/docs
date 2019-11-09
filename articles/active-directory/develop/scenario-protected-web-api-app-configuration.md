---
title: 受保护的 web API-应用代码配置
titleSuffix: Microsoft identity platform
description: 了解如何生成受保护的 Web API 和配置应用程序的代码。
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
ms.openlocfilehash: eb562caf2dfc83841762748f37d1a7ee325de10b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882630"
---
# <a name="protected-web-api-code-configuration"></a>受保护的 web API：代码配置

若要为受保护的 Web API 配置代码，需要了解 API 受保护的含义、如何配置持有者令牌，以及如何验证令牌。

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>ASP.NET/ASP.NET Core API 受保护是什么意思？

与 Web 应用一样，ASP.NET/ASP.NET Core Web API 也“受到保护”，因为它们的控制器操作以 `[Authorize]` 属性为前缀。 因此，仅当使用已授权标识调用 API 时，才能调用控制器操作。

考虑以下问题：

- Web API 如何识别调用它的应用的标识？ （只有应用可以调用 Web API。）
- 如果应用代表某个用户调用 Web API，该用户的标识是什么？

## <a name="bearer-token"></a>持有者令牌

有关应用标识以及有关用户标识（除非 Web 应用程序接受守护程序应用发出的服务到服务调用）的信息保存于调用应用时在标头中设置的持有者令牌内。

以下 C# 代码示例演示了某个客户端在使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 获取令牌后调用 API：

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
> 持有者令牌是客户端应用程序向 *Web API 的* Microsoft 标识平台终结点请求的。 Web API 是唯一应该验证令牌并查看其中包含的声明的应用程序。 客户端应用永远不会尝试检查令牌中的声明。 （Web API 将来可能要求加密令牌。 这项要求会阻止可以查看访问令牌的客户端应用进行访问。）

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

对包含 `[Authorize]` 属性的控制器操作调用某个应用时，ASP.NET/ASP.NET Core 将会查看调用方请求的 Authorization 标头中的持有者令牌，并提取访问令牌。 然后，该令牌将转发到 JwtBearer 中间件，而该中间件会调用适用于 .NET 的 Microsoft IdentityModel 扩展。

在 ASP.NET Core 中，该中间件在 Startup.cs 文件中初始化。

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

该中间件由以下指令添加到 Web API：

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 目前，ASP.NET Core 模板创建了 Azure Active Directory （Azure AD） web Api，这些 Api 在组织或任何组织中登录用户，而不是通过个人帐户登录。 但是，你可以通过在 Startup.cs 文件中添加以下代码，轻松将这些模板更改为使用 Microsoft 标识平台终结点：

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

与 Web 应用中的 OpenID Connect 中间件一样，JwtBearer 中间件将由 `TokenValidationParameters` 定向以验证令牌。 将解密该令牌（如果需要），提取声明，并验证签名。 然后，该中间件将通过检查以下数据来验证该令牌：

- 它面向 Web API（受众）。
- 它是针对可以调用 Web API 的应用颁发的（使用者）。
- 它是由受信任的安全令牌服务器 (STS) 颁发的（颁发者）。
- 它的生存期在有效范围内（过期时间）。
- 它未被篡改（签名）。

此外还可以执行特殊验证。 例如，可以验证（嵌入在令牌中的）签名密钥是否受信任，以及令牌是否未重放。 最后，某些协议需要特定的验证。

### <a name="validators"></a>验证程序

验证步骤是在验证器中捕获的，它们全都位于 .NET 开源库的[Microsoft System.identitymodel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)中，位于一个源文件中： [system.identitymodel/验证器。](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

下表描述了验证程序：

| 验证程序 | 说明 |
|---------|---------|
| `ValidateAudience` | 确保该令牌适用于对它进行验证的应用程序（供我使用）。 |
| `ValidateIssuer` | 确保令牌由受信任的 STS 颁发（来自我信任的一方）。 |
| `ValidateIssuerSigningKey` | 确保验证令牌的应用程序信任用于签署令牌的密钥。 （适用于密钥嵌入在令牌中的特殊情况。 通常不需要满足此要求。） |
| `ValidateLifetime` | 确保令牌仍然（或已经）生效。 验证程序将检查令牌的生存期（`notbefore` 和 `expires` 声明）是否在范围内。 |
| `ValidateSignature` | 确保令牌未被篡改。 |
| `ValidateTokenReplay` | 确保令牌未重放。 （适用于某些一次性协议的特殊情况。） |

所有验证程序与 `TokenValidationParameters` 类的属性相关联，其本身已从 ASP.NET/ASP.NET Core 配置初始化。 在大多数情况下，无需更改参数。 有一种情况例外，非单租户应用不适用上述过程。 （即，接受来自任何组织或个人 Microsoft 帐户的用户的 web 应用。）在这种情况下，必须验证颁发者。

## <a name="token-validation-in-azure-functions"></a>Azure Functions 中的令牌验证

还可以在 Azure 函数中验证传入的访问令牌。 可以在 Azure 函数的[Dotnet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)、 [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)和[Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)中找到验证令牌的示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [验证代码中的范围和应用角色](scenario-protected-web-api-verification-scope-app-roles.md)
