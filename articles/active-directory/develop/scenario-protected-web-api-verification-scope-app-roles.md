---
title: 验证受范围和应用角色保护的 Web API | Azure
titleSuffix: Microsoft identity platform
description: 了解如何生成受保护的 Web API 和配置应用程序的代码。
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
ms.openlocfilehash: a4ee2679da5065ab9e9b02d4ddb313fab75e78f7
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845129"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>受保护的 Web API：验证范围和应用角色

本文介绍如何将授权添加到 Web API。 这种保护可确保只有以下对象才能调用 API：

- 代表具有适当范围的用户的应用程序。
- 具有适当应用程序角色的守护程序应用。

> [!NOTE]
> 本文中的代码片段摘自以下可完全正常运行的示例：
>
> - GitHub 上的 [ASP.NET Core Web API 增量教程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37)
> - [ASP.NET Web API 示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

若要保护 ASP.NET 或 ASP.NET Core Web API，必须在下列其中一项中添加 `[Authorize]` 属性：

- 控制器本身（若要保护所有控制器操作）
- API 的单个控制器操作

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

但是，这种保护并不足够。 它只能保证 ASP.NET 和 ASP.NET Core 对该令牌进行验证。 你的 API 需要验证用来调用 API 的令牌是否是使用预期的声明请求的。 具体而言，这些声明需要验证：

- 作用域（如果代表用户调用 API）。 
- 应用角色（如果可从守护程序应用调用 API）。 

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>在代表用户调用的 API 中验证作用域

如果某个客户端应用代表用户调用了你的 API，则该 API 需要请求具有该 API 的特定作用域的持有者令牌。 有关详细信息，请参阅[代码配置 | 持有者令牌](scenario-protected-web-api-app-configuration.md#bearer-token)。

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

`VerifyUserHasAnyAcceptedScope` 方法将执行诸如以下步骤的某些操作：

- 验证是否存在名为 `http://schemas.microsoft.com/identity/claims/scope` 或 `scp` 的声明。
- 验证该声明的值是否包含 API 预期的作用域。

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

前面的[示例代码](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47)适用于 ASP.NET Core。 对于 ASP.NET，只需将 `HttpContext.User` 替换为 `ClaimsPrincipal.Current`，并将声明类型 `"http://schemas.microsoft.com/identity/claims/scope"` 替换为 `"scp"`。 另请参阅本文下文中的代码片段。

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>验证守护程序应用调用的 API 中的应用角色

如果 Web API 由某个[守护程序应用](scenario-daemon-overview.md)调用，该应用应该对该 Web API 拥有应用程序权限。 如[公开应用程序权限（应用角色）](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)中所示，你的 API 将公开此类权限。 一个示例是 `access_as_application` 应用角色。

现在，你需要让 API 验证它收到的令牌是否包含 `roles` 声明，以及此声明是否具有预期的值。 验证代码类似于对委托权限进行验证的代码，不同之处在于，你的控制器操作针对角色进行测试，而非针对作用域进行测试：

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

`ValidateAppRole` 方法可能类似于：

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

这一次的代码片段适用于 ASP.NET。 对于 ASP.NET Core，只需将 `ClaimsPrincipal.Current` 替换为 `HttpContext.User`，并将声明名称 `"roles"` 替换为 `"http://schemas.microsoft.com/ws/2008/06/identity/claims/role"`。 另请参阅本文上文中的代码片段。

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>当 Web API 只能由守护程序应用调用时接受仅限应用的令牌

用户还可以在用户分配模式下使用角色声明，如[如操作指南：在应用程序中添加应用角色并在令牌中接收它们](howto-add-app-roles-in-azure-ad-apps.md)中所示。 如果角色可同时分配给用户和应用，只需选中相应的角色就能让应用以用户身份登录，以及让用户以应用身份登录。 建议为用户和应用声明不同的角色，以避免出现这种混淆。

如果你希望只有守护程序应用能够调用 Web API，请在验证应用角色时添加一个条件，规定该令牌是仅限应用的令牌。

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

选中反向条件将只允许用于将用户登录的应用调用你的 API。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转移到生产环境](scenario-protected-web-api-production.md)
