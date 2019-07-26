---
title: 受保护的 web API-应用代码配置 |Azure Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: b249b99faa62e73b9aa3247f71f88767fca96f01
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488836"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>受保护的 web API:向 API 添加授权

本文介绍如何将授权添加到 web API。 此保护可确保仅通过以下方法调用 API:

- 代表具有正确范围的用户的应用程序。
- 具有适当应用程序角色的守护程序应用。

> [!NOTE]
> 本文中的代码片段摘自以下示例, 这些示例完全正常
>
> - GitHub 上的[ASP.NET Core WEB API 增量教程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37)
> - [ASP.NET Web API 示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

若要保护 ASP.NET/ASP.NET Core web API, 需要在以下其中一项`[Authorize]`上添加属性:

- 如果要保护控制器的所有操作, 则为控制器本身
- API 的单个控制器操作

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

但这种保护还不够。 它仅保证 ASP.NET/ASP.NET 核心将验证令牌。 API 需要验证用于调用 web API 的令牌是否与预期的声明一起请求, 尤其是:

- 如果代表用户调用 API, 则为*范围*。
- *应用角色*(如果可从后台程序应用调用 API)。

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>验证代表用户调用的 Api 中的作用域

如果你的 API 由客户端应用代表用户调用, 则它需要请求具有 API 的特定作用域的持有者令牌。 (请参阅[代码配置 |持有者令牌](scenario-protected-web-api-app-configuration.md#bearer-token)。)

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

`VerifyUserHasAnyAcceptedScope`方法将执行如下操作:

- 验证是否存在名为`http://schemas.microsoft.com/identity/claims/scope`或`scp`的声明。
- 验证声明的值是否包含 API 所需的范围。

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

此[示例代码](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47)用于 ASP.NET Core。 对于 ASP.NET, 只需`HttpContext.User`将`ClaimsPrincipal.Current`替换为, 并将声明`"http://schemas.microsoft.com/identity/claims/scope"`类型`"scp"`替换为。 (另请参阅本文后面的代码片段。)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>验证守护程序应用调用的 Api 中的应用角色

如果 web API 由[守护程序应用](scenario-daemon-overview.md)调用, 则该应用应要求对你的 web api 具有应用程序权限。 我们已在[公开应用程序权限 (应用角色)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)中看到, API 公开了此类权限 (例如`access_as_application` , 应用角色)。
现在, 你需要让 api 验证它收到的令牌是否包含`roles`声明, 以及此声明是否具有预期的值。 执行此验证的代码类似于验证委托权限的代码, 不同之处在于, 验证控制器操作的`scopes`代码不会`roles`测试:

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

此`ValidateAppRole()`方法可能如下所示:

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

这一次, 代码段适用于 ASP.NET。 对于 ASP.NET Core, 只需`ClaimsPrincipal.Current`将`HttpContext.User`替换为, 并`"roles"`将声明名称`"http://schemas.microsoft.com/identity/claims/roles"`替换为。 (另请参阅本文前面的代码片段。)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>如果仅应由守护程序应用调用 web API, 则接受仅限应用的令牌

`roles`声明还用于用户分配模式的用户。 （有关详细信息，请参阅[如何：在应用程序中添加应用角色, 并在令牌](howto-add-app-roles-in-azure-ad-apps.md)中接收这些角色。)因此, 如果角色可以同时分配给这两个角色, 则仅检查角色会允许应用以用户和其他方式登录。 建议为用户和应用声明不同的角色, 以避免这种混乱。

如果要仅允许后台程序应用调用 web API, 请在验证应用角色时添加一个条件, 该令牌为仅限应用的令牌:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

选中 "反向" 条件将仅允许登录用户的应用调用 API。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转移到生产环境](scenario-protected-web-api-production.md)
