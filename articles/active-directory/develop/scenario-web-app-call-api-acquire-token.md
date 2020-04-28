---
title: 在调用 Web API 的 Web 应用中获取令牌 - Microsoft 标识平台 | Azure
description: 了解如何获取调用 Web API 的 Web 应用的令牌
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 40e788099a159e1f60c0af02deccd7e3bef82744
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181726"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>调用 Web API 的 Web 应用：获取应用的令牌

你已构建了客户端应用程序对象。 现在，你将使用它获取令牌来调用 Web API。 在 ASP.NET 或 ASP.NET Core 中，调用 Web API 是在控制器中完成的。

- 使用令牌缓存获取 Web API 的令牌。 若要获取此标记，请调用 MSAL `AcquireTokenSilent`方法（或在中等效）。
- 调用受保护的 API，将访问令牌作为参数传递给它。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

控制器方法受 `[Authorize]` 属性的保护，该属性会强制经身份验证的用户使用 Web 应用。 下面是用于调用 Microsoft Graph 的代码：

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

`ITokenAcquisition` 服务是由 ASP.NET 通过使用依赖项注入来注入的。

下面是 `HomeController` 的操作的简化代码，该操作获取令牌来调用 Microsoft Graph：

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

为了更好地了解此方案所需的代码，请参阅[aspnetcore-webapp](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)教程的第2阶段（[2-1-Web 应用调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步骤。

还有其他复杂的变化形式，例如：

- 调用多个 API。
- 处理增量许可和条件访问。

[3-WebApp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) 教程的第 3 章中涵盖了这些高级步骤。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

适用于 ASP.NET 的代码类似于为 ASP.NET Core 显示的代码：

- 受 [Authorize] 属性保护的控制器操作会提取控制器的 `ClaimsPrincipal` 成员的租户 ID 和用户 ID。 （ASP.NET 使用 `HttpContext.User`。）
- 然后，它会构建一个 MSAL.NET `IConfidentialClientApplication` 对象。
- 最后，它调用机密客户端应用程序的 `AcquireTokenSilent` 方法。

# <a name="java"></a>[Java](#tab/java)

在 Java 示例中，调用 API 的代码位于 [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62) 中的 getUsersFromGraph 方法中。

该方法尝试调用 `getAuthResultBySilentFlow`。 如果用户需要许可更多作用域，则该代码会处理 `MsalInteractionRequiredException` 对象来质询用户。

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

在 Python 示例中，调用 Microsoft Graph 的代码位于 [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62) 中。

该代码将尝试从令牌缓存中获取令牌。 然后，在设置授权标头后，它将调用 Web API。 如果它无法获取令牌，则会将用户重新登录。

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API](scenario-web-app-call-api-call-api.md)
