---
title: 获取用于调用 web Api 的 web 应用中的令牌-Microsoft 标识平台 |Microsoft
description: 了解如何为调用 web Api 的 web 应用获取令牌
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759068"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>用于调用 web Api 的 web 应用：为应用获取令牌

已生成客户端应用程序对象。 现在，你将使用它来获取用于调用 web API 的令牌。 在 ASP.NET 或 ASP.NET Core 中，调用 web API 是在控制器中完成的：

- 使用令牌缓存获取 web API 的令牌。 若要获取此标记，请调用 `AcquireTokenSilent` 方法。
- 调用受保护的 API，并将访问令牌作为参数传递给它。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

控制器方法受 `[Authorize]` 特性保护，该特性强制用户进行身份验证，以便使用 web 应用程序。 下面是调用 Microsoft Graph 的代码：

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

使用依赖关系注入将 `ITokenAcquisition` 服务注入 ASP.NET。

下面是 `HomeController`的操作的简化代码，该代码获取用于调用 Microsoft Graph 的令牌：

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

为了更好地了解此方案所需的代码，请参阅[aspnetcore-webapp](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)教程的第2阶段（[2-1-Web 应用调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步骤。

还有其他复杂的变化形式，例如：

- 调用多个 Api。
- 处理增量许可和条件性访问。

[WebApp-多 api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)教程的第3章介绍了这些高级步骤。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 的代码类似于为 ASP.NET Core 显示的代码：

- 受 [授权] 属性保护的控制器操作会提取控制器的 `ClaimsPrincipal` 成员的租户 ID 和用户 ID。 （ASP.NET 使用 `HttpContext.User`。）
- 然后，它会生成一个 MSAL.NET `IConfidentialClientApplication` 对象。
- 最后，它调用机密客户端应用程序的 `AcquireTokenSilent` 方法。

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 示例中，调用 API 的代码位于[AuthPageController # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)中的 getUsersFromGraph 方法中。

此方法尝试调用 `getAuthResultBySilentFlow`。 如果用户需要同意更多作用域，则该代码会处理 `MsalInteractionRequiredException` 对象以质询用户。

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

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 示例中，调用 Microsoft Graph 的代码位于[py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)中。

此代码尝试从令牌缓存获取令牌。 然后，在设置授权标头后，它将调用 web API。 如果无法获取令牌，则会重新登录用户。

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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API](scenario-web-app-call-api-call-api.md)
