---
title: 受保护的 Web API - 应用代码配置 | Azure
description: 了解如何构建一个受保护的 web API 和配置应用程序的代码。
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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536901"
---
# <a name="protected-web-api-code-configuration"></a>受保护的 web API:代码配置

若要配置受保护 web API 的代码，您需要了解什么定义 Api，为受保护、 如何配置持有者令牌，以及如何验证令牌。

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>为受保护定义 ASP.NET/ASP.NET Core Api？

Web 应用，如 ASP.NET/ASP.NET Core web Api"保护"因为其控制器操作都带有前缀`[Authorize]`属性。 因此仅当使用的身份授权调用 API，可以调用控制器操作。

考虑以下问题：

- Web API 如何知道调用它的应用的标识？ （只有应用可以调用 web API。）
- 如果应用程序调用 web API 代表某个用户，用户的标识是什么？

## <a name="bearer-token"></a>持有者令牌

有关应用程序中的标识以及用户的信息 （除非 web 应用程序接受来自守护程序服务到服务调用），会保留在调用应用程序时设置的标头中的持有者令牌。

下面是C#显示的客户端获得的令牌使用 Microsoft 身份验证库.NET (MSAL.NET) 之后调用该 API 的代码示例：

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
> 持有者令牌是客户端应用程序向 *Web API 的* Microsoft 标识平台终结点请求的。 Web API 是唯一的应用程序，应验证该令牌并查看其包含的声明。 客户端应用程序应永远不会尝试检查令牌中的声明。 （web API 可能需要，在将来的令牌进行加密。 此要求会阻止访问的客户端应用程序可以查看访问令牌。）

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

应用程序上持有的控制器操作的调用时`[Authorize]`ASP.NET/ASP.NET Core 属性调用请求的 Authorization 标头中的持有者令牌并提取访问令牌。 令牌然后转发给 JwtBearer 中间件，它调用用于.NET 的 Microsoft IdentityModel 扩展。

在 ASP.NET Core 中，此中间件初始化 Startup.cs 文件中：

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

此指令的情况下，中间件添加到 web API:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 目前，ASP.NET Core 模板在你的组织或任何组织中的，不能使用个人帐户中的用户中创建 Azure Active Directory (Azure AD) web Api 的签名。 但您可以轻松地更改他们使用 Microsoft 标识平台终结点，通过将此代码添加到 Startup.cs 文件：

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>令牌验证

通过定向 JwtBearer 中间件，如在 web 应用中的 OpenID Connect 中间件`TokenValidationParameters`来验证令牌。 （根据需要） 解密令牌、 提取声明，并验证签名。 中间件会通过检查此数据来验证该令牌：

- 本文的目标 web API （受众）。
- 它发出的应用程序可以调用 web API (sub)。
- 它是由受信任的安全令牌服务 (STS) （颁发者） 颁发的。
- 其生存期内处于范围内 （过期）。
- 它未被篡改 （签名）。

此外还可以执行特殊验证。 例如，就可以验证签名密钥 （当嵌入在令牌中） 是受信任和令牌不在重播。 最后，某些协议需要特定的验证。

### <a name="validators"></a>验证程序

验证步骤中验证程序，都在捕获[适用于.NET 的 Microsoft IdentityModel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)开放源代码库，在一个源文件中：[Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

此表所述验证程序：

| 验证程序 | 描述 |
|---------|---------|
| `ValidateAudience` | 确保该令牌对令牌进行验证 （对我来说） 的应用程序。 |
| `ValidateIssuer` | 可确保令牌已由受信任的 STS 颁发 （来自我信任的人）。 |
| `ValidateIssuerSigningKey` | 验证令牌的信任关系的应用程序可确保用于对令牌进行签名的密钥。 （其中该密钥嵌入到标记中的特殊情况。 通常不需要。） |
| `ValidateLifetime` | 确保仍 （或已） 有效令牌。 验证程序检查令牌的生存期 (`notbefore`和`expires`声明) 处于范围。 |
| `ValidateSignature` | 确保该标记尚未被篡改。 |
| `ValidateTokenReplay` | 可确保不重播令牌。 （对于某些一次性使用协议种特殊情况。） |

所有验证程序与 `TokenValidationParameters` 类的属性相关联，其本身已从 ASP.NET/ASP.NET Core 配置初始化。 在大多数情况下，无需更改参数。 还有一个例外，对于不是单个租户的应用。 (即，web 应用，接受用户从任何组织或个人 Microsoft 帐户。)在这种情况下，必须验证颁发者。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [验证作用域和在代码中的应用程序角色](scenario-protected-web-api-verification-scope-app-roles.md)
