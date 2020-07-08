---
title: 将调用 Web API 的桌面应用移到生产环境 - Microsoft 标识平台 | Azure
description: 了解如何将调用 Web API 的桌面应用移到生产环境
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882873"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>用于调用 Web API 的桌面应用：移到生产环境

本文介绍如何将调用 Web API 的桌面应用移到生产环境。

## <a name="handle-errors-in-desktop-applications"></a>在桌面应用程序中处理错误

你已了解，在不同的流中如何处理静默流的错误，如代码片段所示。 你还发现存在需要交互的情况，如增量许可和条件访问。

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

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>每次应用运行时，Microsoft 个人帐户都需要 reconsent

对于 Microsoft 个人帐户用户，针对每个本机客户端（桌面或移动应用）的同意调用的 reprompting 是预期的行为。 本机客户端标识本质上是不安全的，这与机密客户端应用程序标识相反。 机密客户端应用程序通过 Microsoft 标识平台交换机密，以证明其身份。 Microsoft 标识平台选择通过在应用程序每次获得授权时提示用户进行同意，来缓解消费者服务的安全。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
