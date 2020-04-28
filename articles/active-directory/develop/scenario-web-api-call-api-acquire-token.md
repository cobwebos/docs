---
title: 获取调用 Web API 的 Web API 的令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何生成调用 Web API 的 Web API（要求获取应用的令牌）。
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
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885134"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>调用 Web API 的 Web API：获取应用的令牌

构建客户端应用程序对象后，使用它来获取可用于调用 Web API 的令牌。

## <a name="code-in-the-controller"></a>控制器中的代码

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

下面是在 API 控制器的操作中调用的代码示例。 它调用下游 API（名为 *todolist*）。

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` 类似于[调用 Web API 的 Web API：应用配置](scenario-web-api-call-api-app-configuration.md)中的方案。 `BuildConfidentialClient()` 使用仅包含一个帐户信息的缓存实例化 `IConfidentialClientApplication`。 该帐户由 `GetAccountIdentifier` 方法提供。

`GetAccountIdentifier` 方法使用与 Web API 收到其 JSON Web 令牌 (JWT) 的用户的标识相关联的声明：

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
下面是在 API 控制器的操作中调用的代码示例。 它调用下游 API - Microsoft Graph。

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Python Web API 需要使用一些中间件来验证从客户端接收的持有者令牌。 然后，Web API 可以通过调用 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 方法，使用 MSAL Python 库获取下游 API 的访问令牌。 使用 MSAL Python 演示此流的示例尚不可用。

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API 的 Web API：调用 API](scenario-web-api-call-api-call-api.md)
