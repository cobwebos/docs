---
title: 将桌面应用程序调用 web Api 移动到生产环境-Microsoft 标识平台 |Microsoft
description: 了解如何将用于调用 web Api 的桌面应用移动到生产环境
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
ms.openlocfilehash: c8a9cf0c05d8af14d52bb1efb536dc8bbe7db84d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702124"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>用于调用 web Api 的桌面应用：移动到生产环境

本文介绍如何将用于调用 web Api 的桌面应用移动到生产环境。

## <a name="handle-errors-in-desktop-applications"></a>处理桌面应用程序中的错误

在不同的流中，你已了解如何处理无提示流的错误，如代码段中所示。 你还发现存在需要交互的情况，如增量许可和条件访问。

## <a name="have-the-user-consent-upfront-for-several-resources"></a>让用户提前获取几个资源的许可

> [!NOTE]
> 多个资源的同意适用于 Microsoft 标识平台，但不适用于 Azure Active Directory （Azure AD） B2C。 Azure AD B2C 仅支持管理员许可，而不支持用户同意。

你不能使用 Microsoft 标识平台（v2.0）终结点一次获取多个资源的令牌。 `scopes` 参数只能包含单个资源的作用域。 可以通过使用 `extraScopesToConsent` 参数，确保用户预先同意多个资源。

例如，你可能有两个资源，每个资源有两个作用域：

- `https://mytenant.onmicrosoft.com/customerapi` 范围 `customer.read` 和 `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` 范围 `vendor.read` 和 `vendor.write`

在此示例中，请使用具有 `extraScopesToConsent` 参数的 `.WithAdditionalPromptToConsent` 修饰符。

对于实例：

### <a name="in-msalnet"></a>在 MSAL.NET 中

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

此调用会获取第一个 web API 的访问令牌。

如果需要调用第二个 web API，请调用 `AcquireTokenSilent` API。

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>每次应用运行时，Microsoft 个人帐户都需要 reconsent

对于 Microsoft 个人帐户用户，针对每个本机客户端（桌面或移动应用）的同意调用的 reprompting 是预期的行为。 本机客户端标识本质上是不安全的，这与机密客户端应用程序标识相反。 机密客户端应用程序通过 Microsoft 标识平台交换机密，以证明其身份。 Microsoft 标识平台选择通过在应用程序每次获得授权时提示用户进行同意，来缓解消费者服务的安全。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
