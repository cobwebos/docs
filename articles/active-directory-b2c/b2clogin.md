---
title: 将应用程序和 Api 迁移到 b2clogin.com
titleSuffix: Azure AD B2C
description: 了解在 Azure Active Directory B2C 的重定向 URL 中使用 b2clogin.com。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: db222515dae51fa5e0334430a3578816b1e8813d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367648"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>将 Azure Active Directory B2C 的重定向 URL 设置为 b2clogin.com

在 Azure Active Directory B2C （Azure AD B2C）应用程序中设置标识提供程序以进行注册和登录时，需要指定重定向 URL。 你不应再在应用程序和 Api 中引用*login.microsoftonline.com* 。 相反，请将*b2clogin.com*用于所有新的应用程序，并将现有应用程序从*login.microsoftonline.com*迁移到*b2clogin.com*。

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>弃用 login.microsoftonline.com

2019年12月12日，我们已在 12 **2020 月 12**日的 Azure AD B2C 上公布了 login.microsoftonline.com 支持计划的停用：

[Azure Active Directory B2C 是弃用 login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

弃用 login.microsoftonline.com 适用于2020年12月12日的所有 Azure AD B2C 租户，提供现有租户一（1）年迁移到 b2clogin.com。 2019年12月之后创建的新租户将不接受来自 login.microsoftonline.com 的请求。 所有功能在 b2clogin.com 终结点上保持不变。

弃用 login.microsoftonline.com 不会影响 Azure Active Directory 租户。 此更改仅影响 Azure Active Directory B2C 租户。

## <a name="benefits-of-b2clogincom"></a>B2clogin.com 的优点

使用*b2clogin.com*作为重定向 URL 时：

* Microsoft 服务在 cookie 标头中使用的空间就会减少。
* 重定向 Url 不再需要包含对 Microsoft 的引用。
* 自定义页面中支持 JavaScript 客户端代码（当前为[预览版](user-flow-javascript-overview.md)）。 由于安全限制，如果你使用*login.microsoftonline.com*，则将从自定义页中删除 JavaScript 代码和 HTML 窗体元素。

## <a name="overview-of-required-changes"></a>所需更改的概述

若要将应用程序迁移到*b2clogin.com*，可能需要进行一些修改：

* 将标识提供程序的应用程序中的重定向 URL 更改为引用*b2clogin.com*。
* 更新 Azure AD B2C 应用程序，以便在其用户流和令牌终结点引用中使用*b2clogin.com* 。
* 更新在 CORS 设置中为[用户界面自定义](active-directory-b2c-ui-customization-custom-dynamic.md)定义的任何**允许的来源**。

## <a name="change-identity-provider-redirect-urls"></a>更改标识提供程序重定向 Url

在已创建应用程序的每个标识提供者的网站上，更改所有受信任的 Url 以重定向到 `your-tenant-name.b2clogin.com` 而不是*login.microsoftonline.com*。

你可以将两种格式用于 b2clogin.com 重定向 Url。 第一种方法是通过使用租户 ID （GUID）替代租户域名，使 "Microsoft" 不会出现在 URL 中的任何位置：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

第二个选项以 `your-tenant-name.onmicrosoft.com`的形式使用租户域名。 例如：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

对于这两种格式：

* 将 `{your-tenant-name}` 替换为 Azure AD B2C 租户的名称。
* 删除 URL 中的 `/te` （如果存在）。

## <a name="update-your-applications-and-apis"></a>更新应用程序和 Api

启用 Azure AD B2C 的应用程序和 Api 中的代码可能会在多个位置引用 `login.microsoftonline.com`。 例如，你的代码可能引用了用户流和令牌终结点。 更新以下内容以改为引用 `your-tenant-name.b2clogin.com`：

* 授权终结点
* 令牌终结点
* 令牌颁发者

例如，Contoso 注册/登录策略的授权终结点现在为：

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

有关将基于 OWIN 的 web 应用程序迁移到 b2clogin.com 的详细信息，请参阅[将基于 OWIN 的 WEB API 迁移到 b2clogin.com](multiple-token-endpoints.md)。

有关迁移受 Azure AD B2C 保护的 Azure API 管理 Api，请参阅[使用 Azure AD B2C 保护 AZURE Api 管理 api](secure-api-management.md)的[迁移到 b2clogin.com](secure-api-management.md#migrate-to-b2clogincom)部分。

## <a name="microsoft-authentication-library-msal"></a>Microsoft 身份验证库 (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority 属性

如果使用的是[MSAL.NET][msal-dotnet] v2 或更早版本，请将**ValidateAuthority**属性设置为在客户端实例化时 `false`，以允许重定向到*b2clogin.com*。 此设置对于 MSAL.NET v3 和更高版本不是必需的。

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

如果你使用[的是 JavaScript MSAL][msal-js]：

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>后续步骤

有关将基于 OWIN 的 web 应用程序迁移到 b2clogin.com 的详细信息，请参阅[将基于 OWIN 的 WEB API 迁移到 b2clogin.com](multiple-token-endpoints.md)。

有关迁移受 Azure AD B2C 保护的 Azure API 管理 Api，请参阅[使用 Azure AD B2C 保护 AZURE Api 管理 api](secure-api-management.md)的[迁移到 b2clogin.com](secure-api-management.md#migrate-to-b2clogincom)部分。

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md