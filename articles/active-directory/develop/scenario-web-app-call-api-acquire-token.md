---
title: 调用 Web API 的 Web 应用（获取应用的令牌）- Microsoft 标识平台
description: 了解如何生成调用 Web API 的 Web 应用（获取应用的令牌）
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fd66dcd6e3845aad79ebffb3cad656d0a14c1a6
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720222"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>调用 Web API 的 Web 应用 - 获取应用的令牌

既然已生成客户端应用程序对象，就可以使用它来获取用于调用 web API 的令牌。 在 ASP.NET 或 ASP.NET Core 中，在控制器中调用 web API。 具体操作是：

- 使用令牌缓存获取 Web API 的令牌。 若要获取此令牌，请`AcquireTokenSilent`调用。
- 使用访问令牌调用受保护的 API。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

控制器方法受`[Authorize]`属性保护，该特性强制用户进行身份验证，以使用 Web 应用。 下面是调用 Microsoft Graph 的代码。

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

@No__t-0 服务通过依赖关系注入注入 ASP.NET。


下面是 HomeController 操作的简化代码，该操作获取调用 Microsoft Graph 所需的令牌。

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

// use the access token to call a protected web API
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Add("Authorization", result.CreateAuthorizationHeader());
string json = await client.GetStringAsync(url);
}
```

若要详细了解此方案所需的代码，请参阅[aspnetcore-webapp](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)教程的第2阶段（[2-1-Web 应用调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步骤。

还有很多其他复杂性，例如：

- 调用多个 Api，
- 处理增量许可和条件性访问。

本教程第3章[WebApp-多 api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)中处理这些高级步骤

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 中的情况类似：

- 受 [授权] 属性保护的控制器操作会提取控制器的 `ClaimsPrincipal` 成员的租户 ID 和用户 ID。 （ASP.NET 使用`HttpContext.User`。）
- 然后，它会生成一个 MSAL.NET `IConfidentialClientApplication`。
- 最后，它调用`AcquireTokenSilent`机密客户端应用程序的方法。

此代码类似于为 ASP.NET Core 显示的代码。

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 示例中，调用 API 的代码位于 getUsersFromGraph 方法[AuthPageController # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)中。

它尝试调用 `getAuthResultBySilentFlow`。 如果用户需要同意更多作用域，则该代码会处理 `MsalInteractionRequiredException` 以质询用户。

```java
@RequestMapping("/msal4jsample/graph/users")
    public ModelAndView getUsersFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
            throws Throwable {

        IAuthenticationResult result;
        ModelAndView mav;
        try {
            result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
        } catch (ExecutionException e) {
            if (e.getCause() instanceof MsalInteractionRequiredException) {

                // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
                // so user can consent to new scopes
                String state = UUID.randomUUID().toString();
                String nonce = UUID.randomUUID().toString();

                SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

                String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                        httpRequest.getParameter("claims"),
                        "User.ReadBasic.all",
                        authHelper.getRedirectUriGraphUsers(),
                        state,
                        nonce);

                return new ModelAndView("redirect:" + authorizationCodeUrl);
            } else {

                mav = new ModelAndView("error");
                mav.addObject("error", e);
                return mav;
            }
        }
    // Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 python 示例中，调用 Microsoft graph 的代码位于[py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)中。

它尝试从令牌缓存获取令牌，然后在设置授权标头后调用 eb API。 如果无法这样做，则会对用户重新登录。

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API](scenario-web-app-call-api-call-api.md)
