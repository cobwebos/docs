---
title: " (MSAL.NET) ，获取几个资源的同意 |Microsoft"
titleSuffix: Microsoft identity platform
description: 了解用户如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 获得多个资源的预先同意。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6333d935e1a902ba173017f8149c098f44398955
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165866"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>用户使用 MSAL.NET 获得多个资源的同意
Microsoft 标识平台终结点不允许你一次获取多个资源的令牌。 使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 时，获取令牌方法中的 scopes 参数应仅包含单个资源的范围。 但是，可以通过使用 `.WithExtraScopeToConsent` 生成器方法指定其他范围来预先同意多个资源。

> [!NOTE]
> 获得多个资源的同意适用于 Microsoft 标识平台，但不适用于 Azure AD B2C。 Azure AD B2C 仅支持管理员同意，不支持用户同意。

例如，如果你有两个资源（每个资源有两个范围）：

- \/带有2个作用域 `customer.read` 和)  (的 https：/mytenant.onmicrosoft.com/customerapi `customer.write`
- \/带有2个作用域 `vendor.read` 和)  (的 https：/mytenant.onmicrosoft.com/vendorapi `vendor.write`

应该使用具有 *extraScopesToConsent* 参数的 `.WithExtraScopeToConsent` 修饰符，如以下示例所示：

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

这将为你获得第一个 Web API 的访问令牌。 然后，当你需要访问第二个 Web API 时，可以从令牌缓存中以无提示方式获取令牌：

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
