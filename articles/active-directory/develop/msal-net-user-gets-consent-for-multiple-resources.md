---
title: 获取几个资源的许可（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解用户如何使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）获取几个资源的预先同意。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89afdbed6870b4ce739ed51131def686c41a3015
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921755"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>用户使用 MSAL.NET 获取几个资源的许可
Microsoft 标识平台终结点不允许同时为多个资源获取令牌。 使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）时，获取令牌方法中的作用域参数应该只包含单个资源的作用域。 不过，你可以通过使用 `.WithExtraScopeToConsent` builder 方法指定其他作用域来提前预先同意多个资源。

> [!NOTE]
> 为 Microsoft 标识平台（而不是 Azure AD B2C）获取几个资源的同意。 Azure AD B2C 仅支持管理员许可，而不支持用户同意。

例如，如果有两个资源，每个资源有2个作用域：

- https：\//mytenant.onmicrosoft.com/customerapi （包含2个范围 `customer.read` 和 `customer.write`）
- https：\//mytenant.onmicrosoft.com/vendorapi （包含2个范围 `vendor.read` 和 `vendor.write`）

应使用包含*extraScopesToConsent*参数的 `.WithExtraScopeToConsent` 修饰符，如以下示例中所示：

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

这会获得第一个 web API 的访问令牌。 然后，当需要访问第二个 web API 时，可以从令牌缓存中无提示地获取令牌：

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
