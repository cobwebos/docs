---
title: 将应用程序和 API 迁移到b2clogin.com
titleSuffix: Azure AD B2C
description: 了解在 Azure Active Directory B2C 的重定向 URL 中使用 b2clogin.com。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189984"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>将 Azure Active Directory B2C 的重定向 URL 设置为 b2clogin.com

在 Azure Active Directory B2C (Azure AD B2C) 应用程序中设置用于注册和登录的标识提供者时，需要指定一个重定向 URL。 不应再在应用程序和 API 中引用*login.microsoftonline.com。* 相反，对所有新应用程序使用*b2clogin.com，* 并将现有应用程序从*login.microsoftonline.com*迁移到*b2clogin.com*。

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>弃用login.microsoftonline.com

2019年12月4日，我们宣布定于**2020年12月4日在**Azure AD B2C 中停用login.microsoftonline.com支持：

[Azure 活动目录 B2C 正在弃用login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

login.microsoftonline.com的弃用将在 2020 年 12 月 4 日对所有 Azure AD B2C 租户生效，为现有租户提供一 （1） 年才能迁移到b2clogin.com。 2019 年 12 月 4 日之后创建的新租户将不接受login.microsoftonline.com的请求。 b2clogin.com终结点上的所有功能都保持不变。

login.microsoftonline.com的弃用不会影响 Azure 活动目录租户。 只有 Azure 活动目录 B2C 租户受此更改的影响。

## <a name="benefits-of-b2clogincom"></a>b2clogin.com的好处

当您使用*b2clogin.com*作为重定向 URL 时：

* Microsoft 服务在 cookie 标头中使用的空间就会减少。
* 重定向 URL 不再需要包含对 Microsoft 的引用。
* 自定义页面支持 JavaScript 客户端代码（目前为[预览版](user-flow-javascript-overview.md)）。 由于安全限制，如果使用login.microsoftonline.com，JavaScript 代码和 HTML 表单元素将从自定义页面*login.microsoftonline.com*中删除。

## <a name="overview-of-required-changes"></a>所需的更改概述

可能需要进行一些修改才能将应用程序迁移到*b2clogin.com：*

* 将标识提供程序应用程序中的重定向 URL 更改为引用*b2clogin.com*。
* 更新 Azure AD B2C 应用程序，以在其用户流和令牌终结点引用中使用*b2clogin.com。*
* 更新您在用户界面[自定义](custom-policy-ui-customization.md)的 CORS 设置中定义的任何**允许源**。

## <a name="change-identity-provider-redirect-urls"></a>更改标识提供者重定向 URL

在创建应用程序的每个标识提供程序的网站上，将所有受信任的 URL 更改为重定向到`your-tenant-name.b2clogin.com`，而不是*login.microsoftonline.com*。

有两种格式可用于b2clogin.com重定向 URL。 第一个选项使用租户 ID (GUID) 来替代租户域名，其优点是无需在 URL 中的任何位置显示“Microsoft”：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

第二个选项使用 `your-tenant-name.onmicrosoft.com` 格式的租户域名。 例如：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

对于这两种格式：

* 将 `{your-tenant-name}` 替换为 Azure AD B2C 租户的名称。
* 删除 `/te`（如果 URL 中存在此参数）。

## <a name="update-your-applications-and-apis"></a>更新应用程序和 API

已启用 Azure AD B2C 的应用程序和 API 中的代码可能在多个位置引用了 `login.microsoftonline.com`。 例如，代码可能引用了用户流和令牌终结点。 请更新以下代码，以改为引用 `your-tenant-name.b2clogin.com`：

* 授权终结点
* 令牌终结点
* 令牌颁发者

例如，Contoso 注册/登录策略的机构终结点现在为：

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

有关将基于 OWIN 的 Web 应用程序迁移到b2clogin.com的信息，请参阅[将基于 OWIN 的 Web API 迁移到b2clogin.com](multiple-token-endpoints.md)。

有关迁移受 Azure AD B2C 保护的 Azure API 管理 API，请参阅[使用 Azure AD B2C](secure-api-management.md)[迁移到b2clogin.com](secure-api-management.md#migrate-to-b2clogincom)部分。

## <a name="microsoft-authentication-library-msal"></a>Microsoft 身份验证库 (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority 属性

如果您使用的[是MSAL.NET][msal-dotnet] v2 或更早版本，请将**验证授权**属性`false`设置为客户端实例化以允许重定向到*b2clogin.com*。 在 MSAL.NET v3 和更高版本中不需要此设置。

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

如果使用 [MSAL for JavaScript][msal-js]：

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

有关将基于 OWIN 的 Web 应用程序迁移到b2clogin.com的信息，请参阅[将基于 OWIN 的 Web API 迁移到b2clogin.com](multiple-token-endpoints.md)。

有关迁移受 Azure AD B2C 保护的 Azure API 管理 API，请参阅[使用 Azure AD B2C](secure-api-management.md)[迁移到b2clogin.com](secure-api-management.md#migrate-to-b2clogincom)部分。

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
