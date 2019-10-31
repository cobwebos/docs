---
title: 用于调用 web Api 的桌面应用（转到生产环境）-Microsoft 标识平台
description: 了解如何构建一个可调用 web Api 的桌面应用（转到生产环境）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5331f01c5dc6acf01f567dbe4c332853bf7aa47e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175550"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>用于调用 web Api 的桌面应用-迁移到生产

本文详细介绍了如何进一步改进应用程序并将其移动到生产环境。

## <a name="handling-errors-in-desktop-applications"></a>处理桌面应用程序中的错误

在不同的流中，你已了解如何处理无提示流的错误（如代码段中所示）。 您还发现需要交互的情况（增量许可和条件访问）。

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>如何让用户获得多个资源的前期许可

> [!NOTE]
> 为 Microsoft 标识平台（而不是 Azure Active Directory （Azure AD） B2C）获取几个资源的同意。 Azure AD B2C 仅支持管理员许可，而不支持用户同意。

Microsoft 标识平台（v2.0）终结点不允许同时为多个资源获取令牌。 因此，`scopes` 参数只能包含单个资源的作用域。 可以通过使用 `extraScopesToConsent` 参数，确保用户预先同意多个资源。

例如，如果有两个资源，每个资源有两个作用域：

- `https://mytenant.onmicrosoft.com/customerapi`-具有2个范围 `customer.read` 和 `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`-具有2个范围 `vendor.read` 和 `vendor.write`

应使用具有 `extraScopesToConsent` 参数的 `.WithAdditionalPromptToConsent` 修饰符。

对于实例：

### <a name="in-msalnet"></a>在 MSAL.NET 中

```CSharp
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

### <a name="in-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL

Objective-C：

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift：

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

此调用会获得第一个 web API 的访问令牌。

需要调用第二个 web API 时，可以调用 `AcquireTokenSilent` API：

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>每次运行应用时，Microsoft 个人帐户都需要 reconsenting

对于 Microsoft 个人帐户用户，针对每个本机客户端（桌面/移动应用）调用的 reprompting 许可是预期的行为。 本机客户端标识本质上是不安全的（与使用 Microsoft 标识平台交换机密以证明其身份的机密客户端应用程序相反）。 Microsoft 标识平台通过在每次授权应用程序时提示用户同意，为消费者服务选择缓解此安全。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
