---
title: 验证范围和应用程序角色受保护的 Web API |Microsoft
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768113"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>受保护的 web API：验证范围和应用角色

本文介绍如何将授权添加到 web API。 此保护可确保仅通过以下方法调用 API：

- 代表具有正确范围的用户的应用程序。
- 具有适当应用程序角色的守护程序应用。

> [!NOTE]
> 本文中的代码片段摘自以下示例，它们完全正常运行：
>
> - GitHub 上的[ASP.NET Core WEB API 增量教程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37)
> - [ASP.NET Web API 示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

若要保护 ASP.NET 或 ASP.NET Core web API，必须将 `[Authorize]` 特性添加到以下项之一：

- 如果要保护所有控制器操作，则为控制器本身
- API 的单个控制器操作

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

但这种保护还不够。 它仅保证 ASP.NET 和 ASP.NET Core 验证令牌。 API 需要验证用于调用 API 的令牌是否与预期的声明一起请求。 这些声明特别需要验证：

- 如果代表用户调用 API，则为*范围*。
- *应用角色*（如果可从守护程序应用调用 API）。

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>验证代表用户调用的 Api 中的作用域

如果客户端应用代表用户调用 API，则 API 需要请求具有 API 的特定作用域的持有者令牌。 有关详细信息，请参阅[代码配置 |持有者令牌](scenario-protected-web-api-app-configuration.md#bearer-token)。

```csharp
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

`VerifyUserHasAnyAcceptedScope` 方法执行类似于以下步骤的操作：

- 验证是否存在名为 `http://schemas.microsoft.com/identity/claims/scope` 或 `scp`的声明。
- 验证声明的值是否包含 API 所需的范围。

```csharp
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

前面的[示例代码](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47)用于 ASP.NET Core。 对于 ASP.NET，只需将 `HttpContext.User` 替换为 `ClaimsPrincipal.Current`，然后将声明类型 `"http://schemas.microsoft.com/identity/claims/scope"` 替换 `"scp"`。 另请参阅本文后面的代码片段。

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>验证守护程序应用调用的 Api 中的应用角色

如果 web API 由[守护程序应用](scenario-daemon-overview.md)调用，则该应用应要求对你的 web api 具有应用程序权限。 如[公开应用程序权限（应用程序角色）](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)所示，你的 API 将公开此类权限。 一个示例是 `access_as_application` 应用程序角色。

你现在需要让 API 验证它收到的令牌是否包含 `roles` 声明，以及此声明是否具有预期值。 验证代码类似于验证委托权限的代码，不同之处在于控制器操作会测试角色而不是作用域：

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole` 方法可能如下所示：

```csharp
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

这一次，代码段适用于 ASP.NET。 对于 ASP.NET Core，只需将 `ClaimsPrincipal.Current` 替换为 `HttpContext.User`，并将 `"roles"` 声明名称替换为 `"http://schemas.microsoft.com/identity/claims/roles"`。 另请参阅本文前面的代码片段。

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>如果仅应由守护程序应用调用 web API，则接受仅限应用的令牌

用户还可以在用户分配模式下使用角色声明，如[如何：在应用程序中添加应用程序角色和在令牌中接收这些](howto-add-app-roles-in-azure-ad-apps.md)角色中所示。 如果角色可同时分配给两者，则检查角色将允许应用以用户和用户身份登录，以作为应用登录。 建议为用户和应用声明不同的角色，以避免这种混乱。

如果只想要守护程序应用调用 web API，请在验证应用角色时添加令牌为仅限应用的令牌的条件。

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

如果选中 "反向" 条件，则仅允许登录用户的应用调用 API。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到生产](scenario-protected-web-api-production.md)
