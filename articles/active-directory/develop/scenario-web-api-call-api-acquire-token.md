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
ms.openlocfilehash: ab0b74ffbcd8167613c6a8470e2f9102566edc60
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257225"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>调用 Web API 的 Web API：获取应用的令牌

构建客户端应用程序对象后，使用它来获取可用于调用 Web API 的令牌。

## <a name="code-in-the-controller"></a>控制器中的代码

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft* 将添加扩展方法，这些扩展方法为调用 Microsoft Graph 或下游 Web API 提供便利服务。 这些方法在 [调用 Web api 的 WEB api](scenario-web-api-call-api-call-api.md)中进行了详细说明：调用 API。 使用这些帮助器方法，你无需手动获取令牌。

但是，如果你确实想要手动获取令牌，以下代码显示了一个示例，说明如何使用在 API 控制器中执行此操作 *。* 它调用下游 API（名为 *todolist*）。
若要获取令牌以调用下游 API，可以通过控制器构造函数中（如果使用 Blazor，则在页面构造函数中）的依赖项注入，来注入 `ITokenAcquisition` 服务，并在控制器操作中使用该服务，从而为用户 (`GetAccessTokenForUserAsync`)，或如果是在守护程序方案下则为应用程序本身 (`GetAccessTokenForAppAsync`) 获取令牌。

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

有关 `callTodoListService` 方法的详细信息，请参阅[调用 Web API 的 Web API：调用 API](scenario-web-api-call-api-call-api.md)。

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

Python web API 需要使用中间件来验证从客户端接收的持有者令牌。 然后，web API 可以通过调用方法来使用 MSAL Python 库获取下游 API 的访问令牌 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 。 使用 MSAL Python 演示此流的示例尚不可用。

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API 的 Web API：调用 API](scenario-web-api-call-api-call-api.md)
