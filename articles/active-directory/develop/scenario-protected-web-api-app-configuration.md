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
ms.openlocfilehash: 3f07105c14d4dafeb689eaaf7d679f93e5f235fe
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377212"
---
# <a name="protected-web-api-code-configuration"></a>受保护的 web API：代码配置

若要为受保护的 web API 配置代码，需要了解：

- 什么定义受保护的 Api。
- 如何配置持有者令牌。
- 如何验证令牌。

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>什么定义受保护的 ASP.NET 和 ASP.NET Core Api？

与 web 应用一样，ASP.NET 和 ASP.NET Core web Api 受到保护，因为其控制器操作以 **[授权]** 特性为前缀。 仅当使用授权标识调用 API 时，才能调用控制器操作。

请考虑以下问题：

- 只有应用可以调用 web API。 API 如何识别调用它的应用的标识？
- 如果应用代表用户调用 API，用户的标识是什么？

## <a name="bearer-token"></a>持有者令牌

当调用应用时，标头中设置的持有者令牌包含有关应用标识的信息。 它还保存有关用户的信息，除非 web 应用接受来自守护程序应用的服务到服务调用。

下面是一个C#代码示例，该示例演示了在使用适用于 .Net 的 Microsoft 身份验证库（MSAL.NET）获取令牌后调用 API 的客户端：

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
> 客户端应用程序请求将持有者令牌发送到*WEB API*的 Microsoft 标识平台终结点。 Web API 是唯一应该验证令牌并查看其包含的声明的应用程序。 客户端应用程序不应尝试检查令牌中的声明。
>
> 将来，web API 可能需要加密令牌。 此要求会阻止访问可查看访问令牌的客户端应用。

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

在保存了 **[authorization]** 特性的控制器操作上调用应用程序时，ASP.NET 和 ASP.NET Core 从授权标头的持有者令牌中提取访问令牌。 然后将访问令牌转发到 JwtBearer 中间件，该中间件会调用 Microsoft System.identitymodel Extensions for .NET。

在 ASP.NET Core 中，此中间件在 Startup.cs 文件中进行初始化。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

此指令将中间件添加到 web API：

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 目前，ASP.NET Core 模板创建了 Azure Active Directory （Azure AD） web Api，该 Api 用于登录组织或任何组织中的用户。 他们不会通过个人帐户登录用户。 但你可以通过将以下代码添加到 Startup.cs，将模板更改为使用 Microsoft 标识平台终结点：

```csharp
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

前面的代码段摘自 WebApiServiceCollectionExtensions [/# L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)中的 ASP.NET CORE web API 增量教程。 从 Startup.cs 调用**AddProtectedWebApi**方法，该方法不是代码片段所示。

## <a name="token-validation"></a>令牌验证

在前面的代码片段中，JwtBearer 中间件（如 web apps 中的 OpenID Connect 中间件）将根据 `TokenValidationParameters`的值来验证令牌。 令牌根据需要进行解密，提取声明，并验证签名。 然后，中间件通过检查此数据来验证令牌：

- 受众：令牌针对 web API。
- Sub：对允许调用 web API 的应用程序发出此操作。
- 颁发者：它是由受信任的 security token service （STS）颁发的。
- 过期：其生存期在范围内。
- 签名：未被篡改。

还可以进行特殊验证。 例如，可以验证签名密钥在嵌入令牌时是否受信任，以及该令牌是否未被重播。 最后，某些协议需要进行特定验证。

### <a name="validators"></a>验证程序

验证步骤是在验证器中捕获的，它们由[适用于 .net 的 Microsoft System.identitymodel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)提供。 验证程序在库源文件[system.identitymodel/验证](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)器中定义。

下表描述了验证程序：

| 基值 | 说明 |
|---------|---------|
| **ValidateAudience** | 确保令牌适用于为你验证令牌的应用程序。 |
| **ValidateIssuer** | 确保令牌由受信任的 STS 颁发，这意味着它来自你信任的人。 |
| **ValidateIssuerSigningKey** | 确保验证令牌的应用程序信任用于对令牌进行签名的密钥。 有一种特殊情况，该键嵌入到了标记中。 但这种情况通常不会出现。 |
| **ValidateLifetime** | 确保标记仍有效。 验证程序检查令牌的生存期是否在**notbefore**和**expires**声明指定的范围内。 |
| **ValidateSignature** | 确保标记未被篡改。 |
| **ValidateTokenReplay** | 确保标记不会重播。 某些一次性协议有一种特殊情况。 |

验证程序与**TokenValidationParameters**类的属性相关联。 属性从 ASP.NET 和 ASP.NET Core 配置中进行初始化。

在大多数情况下，无需更改参数。 不是单个租户的应用程序除外。 这些 web 应用将接受来自任何组织或个人 Microsoft 帐户的用户。 在这种情况下，必须验证颁发者。

## <a name="token-validation-in-azure-functions"></a>Azure Functions 中的令牌验证

你还可以在 Azure Functions 中验证传入的访问令牌。 可在[Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)、 [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)和[Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)中找到此类验证的示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [验证代码中的作用域和应用程序角色](scenario-protected-web-api-verification-scope-app-roles.md)
