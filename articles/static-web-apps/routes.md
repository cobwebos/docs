---
title: Azure 静态 Web 应用中的路由
description: 了解后端路由规则以及如何用角色保护路由。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4a9639343827ebc5bb17a6d62d9b65d0b561e932
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595126"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Azure 静态 Web 应用预览中的路由

Azure 静态 Web 应用中的路由定义静态内容和 API 的后端路由规则和授权行为。 规则在 routes.json 文件中定义为一组规则。

- routes.json 文件必须存在于应用生成工件文件夹的根目录中。
- 规则按照其在 `routes` 数组中显示的顺序执行。
- 规则评估将在第一次匹配时停止。 路由规则不会链接在一起。
- 角色在 routes.json 文件中定义，并且用户通过[邀请](authentication-authorization.md)关联到角色。
- 你可以完全控制角色名称。

路由主题明显与身份验证和授权概念重叠。 务必要阅读[身份验证和授权](authentication-authorization.md)指南以及本文。

## <a name="location"></a>位置

routes.json 文件必须存在于应用生成工件文件夹的根目录中。 如果 Web 应用包含将生成的文件从特定文件夹复制到生成工件文件夹的生成步骤，则 routes.json 文件需要存在于该特定文件夹中。

下表列出了在其中放置若干前端 JavaScript 框架和库的 routes.json 文件的相应位置。

|框架/库 | 位置  |
|---------|----------|
| Angular | _assets_   |
| React   | _public_  |
| Svelte  | _public_   |
| Vue     | _public_ |

## <a name="defining-routes"></a>定义路由

路由在 routes.json 文件中定义为 `routes` 属性上的路由规则数组。 每个规则都包含一个路由模式，以及一个或多个可选的规则属性。 有关用法示例，请参阅[路由文件示例](#example-route-file)。

| 规则属性  | 必选 | 默认值 | 注释                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | 是      | 不适用          | 调用方请求的路由模式。<ul><li>路由路径的末尾支持[通配符](#wildcards)。 例如，路由 admin/\* 与 admin 路径下的任何路由匹配。<li>路由的默认文件为 index.html。</ul>|
| `serve`        | 否       | 不适用          | 定义从请求返回的文件或路径。 文件路径和名称可以不同于所请求的路径。 如果定义了 `serve` 值，则使用请求的路径。 |
| `allowedRoles` | 否       | 匿名     | 角色名称数组。 <ul><li>有效字符包括 `a-z`、`A-Z`、`0-9` 和 `_`。<li>内置角色 `anonymous` 适用于所有未经身份验证的用户。<li>内置角色 `authenticated` 适用于任何已登录的用户。<li>用户必须至少属于一个角色。<li>角色在 OR 基础上进行匹配。 如果用户处于列出的任何角色中，则授予访问权限。<li>单个用户通过[邀请](authentication-authorization.md)关联到角色。</ul> |
| `statusCode`   | 否       | 200           | 响应请求的 [HTTP 状态代码](https://wikipedia.org/wiki/List_of_HTTP_status_codes)。 |

## <a name="securing-routes-with-roles"></a>使用角色保护路由

通过将一个或多个角色名称添加到规则的 `allowedRoles` 数组中来保护路由。 有关用法示例，请参阅[路由文件示例](#example-route-file)。

默认情况下，每个用户都属于内置 `anonymous` 角色，所有登录用户都是 `authenticated` 角色成员。 例如，若要将路由限制为仅经过身份验证的用户，请将内置 `authenticated` 角色添加到 `allowedRoles` 数组。

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

可以根据需要在 `allowedRoles` 数组中创建新角色。 若要将路由限制为仅限管理员，可以在 `allowedRoles` 数组中定义 `administrator` 角色。

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- 你可以完全控制角色名称；不存在角色必须遵循的主列表。
- 单个用户通过[邀请](authentication-authorization.md)关联到角色。

## <a name="wildcards"></a>通配符

通配符规则与给定路由模式下的所有请求相匹配。 如果在规则中定义 `serve` 值，则命名的文件或路径将作为响应提供。

例如，若要实现日历应用程序的路由，可以映射日历路由下的所有 URL，以提供单个文件。

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

然后，calendar.html 文件可使用客户端路由为 URL 变体（如 `/calendar/january/1`、`/calendar/2020` 和 `/calendar/overview`）提供不同的视图。

还可以用通配符保护路由。 在下面的示例中，admin 路径下请求的任何文件都需要一个已经过身份验证的用户，该用户是管理员角色的成员。

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> 对服务的文件所引用文件的请求仅针对身份验证检查进行评估。 例如，对通配符路径下 CSS 文件的请求将提供 CSS 文件，而不是提供定义为 `serve` 的文件。 只要用户满足所需的身份验证要求，就会提供 CSS 文件。

## <a name="fallback-routes"></a>回退路由

前端 JavaScript 框架或库通常依赖于 Web 应用导航的客户端路由。 这些客户端路由规则无需向服务器发回请求即可更新浏览器的窗口位置。 如果刷新页面，或直接导航到客户端路由规则生成的位置，则需要服务器端回退路由来提供相应的 HTML 页面。

下面的示例演示了一个常见的回退路由：

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

必须在路由规则最后列出回退路由，因为它会捕获先前定义的规则未捕获的所有请求。

## <a name="redirects"></a>重定向

可以使用 [301](https://en.wikipedia.org/wiki/HTTP_301) 和 [302](https://en.wikipedia.org/wiki/HTTP_302) HTTP 状态代码，以将请求重定向到另一个路由。

例如，下面的规则创建从 old-page.html 到 new-page.html 的 301 重定向。

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

重定向还适用于未定义不同文件的路径。

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>自定义错误页

用户可能会遇到多个可能导致错误的不同情况。 使用 `platformErrorOverrides` 数组，可以提供自定义体验来响应这些错误。 请参阅[示例路由文件](#example-route-file)，以便将数组放置在 routes.json 文件中。

下表列出了可用的平台错误替代：

| 错误类型  | HTTP 状态代码 | 说明 |
|---------|---------|---------|
| `NotFound` | 404  | 在服务器上找不到页面。 |
| `Unauthenticated` | 401 | 用户未使用[身份验证提供程序](authentication-authorization.md)登录。 |
| `Unauthorized_InsufficientUserInformation` | 401 | 身份验证提供程序上的用户帐户未配置为公开所需的数据。 当应用向用户的电子邮件地址请求身份验证提供程序，但用户选择限制对电子邮件地址的访问权限时，可能会发生此错误。 |
| `Unauthorized_InvalidInvitationLink` | 401 | 邀请已过期，或用户点击了为另一位收件人生成的邀请链接。  |
| `Unauthorized_MissingRoles` | 401 | 用户不是所需角色的成员。 |
| `Unauthorized_TooManyUsers` | 401 | 站点已达到用户的最大数量，服务器正在限制进一步增加。 此错误会向客户端公开，因为你可以生成的[邀请](authentication-authorization.md)数没有限制，某些用户可能永远不会接受邀请。|
| `Unauthorized_Unknown` | 401 | 尝试对用户进行身份验证时出现未知问题。 此错误的一个原因可能是用户未被识别，因为他们未向应用程序授予许可。|

## <a name="example-route-file"></a>路由文件示例

下面的示例演示如何在 routes.json 文件中生成静态内容和 API 的路由规则。 某些路由使用 [/.auth 系统文件夹](authentication-authorization.md)来访问与身份验证相关的终结点。

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "serve": "/login"
    }
  ]
}
```

下面的示例说明当请求与规则匹配时将发生的情况。

|请求...  | 结果为... |
|---------|---------|---------|
| /profile | 向经过身份验证的用户提供 /profile/index.html 文件。 未经身份验证的用户重定向到 /login。 |
| /admin/reports | 向经过身份验证的管理员角色用户提供 /admin/reports/index.html 文件。 经过身份验证的非管理员角色的用户将收到 401 错误<sup>1</sup>。 未经身份验证的用户重定向到 /login。 |
| /api/admin | 将经过身份验证的管理员角色用户发出的请求发送到 API。 经过身份验证的非管理员角色的用户，和未经身份验证的用户将收到 401 错误。 |
| /customers/contoso | 向经过身份验证的管理员或 customers\_contoso 角色用户提供 /customers/contoso/index.html 文件<sup>1</sup>。 经过身份验证的非管理员或非 customers\_contoso 角色的用户将收到 401 错误。 未经身份验证的用户重定向到 /login。 |
| /login     | 未经身份验证的用户将面临在 GitHub 中进行身份验证的挑战。 |
| /.auth/login/twitter     | 已禁用通过 Twitter 的授权。 服务器响应时出现 404 错误。 |
| /logout     | 用户已注销任何身份验证提供程序。 |
| /calendar/2020/01 | 将向浏览器提供 /calendar.html 文件。 |
| /specials | 浏览器将重定向到 /deals。 |
| /unknown-folder     | 提供 /custom-404.html 文件。 |

<sup>1</sup> 可以通过在 `platformErrorOverrides` 数组中定义 `Unauthorized_MissingRoles` 规则来提供自定义错误页。

## <a name="restrictions"></a>限制

- routes.json 文件不能超过 100 KB
- routes.json 文件最多支持 50 个不同的角色

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用程序身份验证和授权](authentication-authorization.md)
