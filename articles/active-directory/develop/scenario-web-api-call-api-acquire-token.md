---
title: 获取用于调用 web Api 的 web API 的标记 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建一个可调用 web Api 的 web API，该 API 需要获取应用的令牌。
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2721837459af24f39bb15ee17d394345cbb37eb1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834104"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>用于调用 web Api 的 web API：获取应用的令牌

生成客户端应用程序对象后，使用该对象获取可用于调用 web API 的令牌。

## <a name="code-in-the-controller"></a>控制器中的代码

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

下面是在 API 控制器的操作中调用的代码示例。 它调用名为*todolist*的下游 API。

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

`BuildConfidentialClient()` 类似于用于[调用 Web api 的 WEB api 中的方案：应用配置](scenario-web-api-call-api-app-configuration.md)。 `BuildConfidentialClient()` 使用只包含一个帐户的信息的缓存来实例化 `IConfidentialClientApplication`。 帐户由 `GetAccountIdentifier` 方法提供。

`GetAccountIdentifier` 方法使用与用户的标识关联的声明，web API 将为该用户接收 JSON Web 令牌（JWT）：

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

# <a name="javatabjava"></a>[Java](#tab/java)
下面是在 API 控制器的操作中调用的代码示例。 它调用下游 API Microsoft Graph。

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Python web API 需要使用一些中间件来验证从客户端接收的持有者令牌。 然后，web API 可以通过调用[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of)方法，使用 MSAL Python 库获取下游 API 的访问令牌。 使用 MSAL Python 演示此流的示例尚不可用。

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于调用 web Api 的 web API：调用 API](scenario-web-api-call-api-call-api.md)
