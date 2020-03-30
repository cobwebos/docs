---
title: 将调用 Web API 的桌面应用移到生产环境 - Microsoft 标识平台 | Azure
description: 了解如何将调用 Web API 的桌面应用移到生产环境
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262563"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>调用 Web API 的桌面应用：移动到生产

本文介绍如何将调用 Web API 的桌面应用移到生产环境。

## <a name="handle-errors-in-desktop-applications"></a>在桌面应用程序中处理错误

你已了解，在不同的流中如何处理静默流的错误，如代码片段所示。 您还看到，在某些情况下需要交互，如增量同意和条件访问。

## <a name="have-the-user-consent-upfront-for-several-resources"></a>让用户提前许可多个资源

> [!NOTE]
> 获得多个资源的同意适用于 Microsoft 标识平台，但不适用于 Azure Active Directory (Azure AD) B2C。 Azure AD B2C 仅支持管理员同意，不支持用户同意。

不能使用 Microsoft 标识平台 (v2.0) 终结点一次获取多个资源的令牌。 `scopes` 参数只能包含单个资源的范围。 可以使用 `extraScopesToConsent` 参数确保用户预先同意多个资源。

例如，你可能有两个资源（每个资源有两个范围）：

- `https://mytenant.onmicrosoft.com/customerapi`，范围为 `customer.read` 和 `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`，范围为 `vendor.read` 和 `vendor.write`

在此示例中，请使用具有 `extraScopesToConsent` 参数的 `.WithAdditionalPromptToConsent` 修饰符。

例如：

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

### <a name="in-msal-for-ios-and-macos"></a>在适用于 iOS 和 macOS 的 MSAL 中

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

此调用为你获得第一个 Web API 的访问令牌。

如果需要调用第二个 Web API，请调用 `AcquireTokenSilent` API。

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>每次应用运行时，Microsoft 个人帐户都需要重新批准

对于 Microsoft 个人帐户用户，在授权的每个本机客户端（桌面或移动应用）调用上重新提示同意是预期行为。 本机客户端标识本质上是不安全的，这与机密客户端应用程序标识相悖。 机密客户端应用程序与 Microsoft 标识平台交换机密，以证明其身份。 Microsoft 标识平台选择在每次授权应用程序时提示用户同意，以减轻消费者服务的这种不安全。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
