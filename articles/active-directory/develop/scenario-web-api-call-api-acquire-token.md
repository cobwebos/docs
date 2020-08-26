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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7e0701cc5a9bb14800a48e2281dba1eb6ea0cf72
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026452"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>调用 Web API 的 Web API：获取应用的令牌

构建客户端应用程序对象后，使用它来获取可用于调用 Web API 的令牌。

## <a name="code-in-the-controller"></a>控制器中的代码

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

下面是使用在 API 控制器的操作中调用的使用 Microsoft 的代码示例。 它调用下游 API（名为 *todolist*）。 若要获取令牌以调用下游 API，你可以在 `ITokenAcquisition` 控制器的构造函数中插入服务（如果你使用 Blazor，则在你的页面构造函数中），并在控制器操作中使用它（ `GetAccessTokenForUserAsync` ），或者在后台应用程序的情况下为应用程序本身（）获取令牌 `GetAccessTokenForAppAsync` 。

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

有关方法的详细信息 `callTodoListService` ，请参阅用于[调用 web api 的 web Api：调用 API](scenario-web-api-call-api-call-api.md)。

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

Python Web API 需要使用一些中间件来验证从客户端接收的持有者令牌。 然后，Web API 可以通过调用 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 方法，使用 MSAL Python 库获取下游 API 的访问令牌。 我们尚未编写在 MSAL Python 中演示此流的示例。

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于调用 web Api 的 web API：调用 API](scenario-web-api-call-api-call-api.md)
