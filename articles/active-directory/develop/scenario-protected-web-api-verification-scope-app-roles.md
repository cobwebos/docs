---
title: 受保护的 web API 的应用程序代码配置 |Azure Active Directory
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
ms.openlocfilehash: 23ff03316a1f9409d4d6e4b7ddf52d0c8cc7a909
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551542"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>受保护的 web API:将授权添加到你的 API

本文介绍如何将授权添加到 web API。 这种保护可确保仅可由调用 API:

- 代表具有适当范围的用户的应用程序。
- 具有正确的应用程序角色的守护程序应用。

若要保护的 ASP.NET/ASP.NET Core web API，你将需要添加`[Authorize]`其中一种属性：

- 控制器本身，如果你想要保护的控制器的所有操作
- 用于 API 的单独的控制器操作

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

但这种保护还不够。 它只保证 ASP.NET/ASP.NET Core 将验证令牌。 你的 API 需要验证用于调用的 web API 请求的声明的标记它需要，特别是：

- *作用域*，如果代表用户调用 API。
- *应用程序角色*，则可以从守护程序应用调用 API。

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>正在验证中代表用户调用 Api 的作用域

如果客户端应用代表用户调用 API，则它需要请求具有特定作用域 api 的持有者令牌。 (请参阅[代码配置 |持有者令牌](scenario-protected-web-api-app-configuration.md#bearer-token)。)

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope`方法将执行如下操作：

- 检查是否存在名为的声明`http://schemas.microsoft.com/identity/claims/scope`或`scp`。
- 验证声明包含所需的 API 的作用域的值。

```CSharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

此示例代码适用于 ASP.NET Core。 对于 ASP.NET，只需替换`HttpContext.User`与`ClaimsPrincipal.Current`，并替换的声明类型`"http://schemas.microsoft.com/identity/claims/scope"`与`"scp"`。 （请参阅本文后面的代码段。）

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>验证 Api 调用的守护程序应用中的应用程序角色

如果你的 web API 调用[守护程序应用](scenario-daemon-overview.md)，该应用应要求对你的 web API 应用程序权限。 我们在中所见[公开应用程序权限 （应用角色）](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) API 公开此类权限 (例如，`access_as_application`应用角色)。
现在需要能够验证它收到的令牌包含 Api`roles`声明和此声明具有其预期的值。 执行此验证的代码是验证委派的权限，不同之处在于，而不是用于测试的代码相似`scopes`，用于将测试控制器操作`roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()`方法可以为下列内容：

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage 
        { StatusCode = HttpStatusCode.Unauthorized, 
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found" 
        });
    }
}
}
```

此示例代码适用于 ASP.NET。 适用于 ASP.NET Core，只需替换`ClaimsPrincipal.Current`与`HttpContext.User`，并替换`"roles"`声明名称与`"http://schemas.microsoft.com/identity/claims/roles"`。 （请参阅本文前面部分的代码段。）

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>如果应仅由守护程序应用调用 web API，接受仅限应用的令牌

`roles`声明也可用于用户分配模式中的用户。 （有关详细信息，请参阅[如何：在你的应用程序中添加应用角色和令牌中收到它们](howto-add-app-roles-in-azure-ad-apps.md)。)如果角色是可分配给这两，所以只需检查角色将允许应用中，用户和其他的方法，以登录。 我们建议您声明的不同角色的用户和应用，以防止这种混乱。

如果你想要仅允许守护程序应用调用 web API，添加条件时验证应用程序角色，, 该令牌是否仅限应用的令牌：

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

检查反的条件将允许登录用户来调用 API 的应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转移到生产环境](scenario-protected-web-api-production.md)
