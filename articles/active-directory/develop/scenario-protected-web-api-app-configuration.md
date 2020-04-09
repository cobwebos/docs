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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882465"
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
> 客户端应用程序向 Web API 的 Microsoft 标识平台终结点请求持有者令牌。** Web API 是唯一应该验证令牌并查看其中包含的声明的应用程序。 客户端应用永远不会尝试检查令牌中的声明。
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

### <a name="code-initialization"></a>代码初始化

对包含 **[Authorize]** 属性的控制器操作调用某个应用时，ASP.NET 和 ASP.NET Core 将从 Authorization 标头的持有者令牌中提取访问令牌。 然后，该访问令牌将转发到 JwtBearer 中间件，而该中间件会调用适用于 .NET 的 Microsoft IdentityModel 扩展。

在 ASP.NET Core 中，该中间件在 Startup.cs 文件中初始化。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

该中间件由以下指令添加到 Web API：

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 目前，ASP.NET Core 模板会创建可将你的组织或任何组织中的用户登录的 Azure Active Directory (Azure AD) Web API。 他们不使用个人帐户登录用户。 但是，你可以通过在 Startup.cs 中添加以下代码，将这些模板更改为使用 Microsoft 标识平台终结点：

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

上述代码片段摘自 [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63) 中的 ASP.NET Core Web API 增量教程。 **AddProtectedWebApi** 方法是从 Startup.cs 调用的，代码片段中并未演示它的全部作用。

## <a name="token-validation"></a>令牌验证

在上述代码片段中，与 Web 应用中的 OpenID Connect 中间件一样，JwtBearer 中间件将会根据 `TokenValidationParameters` 的值验证令牌。 根据需要解密该令牌，提取声明，并验证签名。 然后，该中间件将通过检查以下数据来验证该令牌：

- 访问群体：令牌是 Web API 的目标。
- Sub：它是为允许调用 Web API 的应用发布的。
- 颁发者：它由受信任的安全令牌服务 （STS） 颁发。
- 过期：其生存期在范围内。
- 签名：没有被篡改。

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

验证程序与 **TokenValidationParameters** 类的属性相关联。 这些属性从 ASP.NET 和 ASP.NET Core 配置初始化。

在大多数情况下，无需更改参数， 非单租户应用除外。 这些 Web 应用接受来自任何组织或个人 Microsoft 帐户的用户。 对于这种情况，必须验证颁发者。

## <a name="token-validation-in-azure-functions"></a>Azure Functions 中的令牌验证

还可以在 Azure Functions 中验证传入的访问令牌。 可以在 [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)、[NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) 和 [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) 中找到此类验证的示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [验证代码中的范围和应用角色](scenario-protected-web-api-verification-scope-app-roles.md)
