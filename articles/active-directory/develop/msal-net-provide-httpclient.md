---
title: 提供 HttpClient 和代理 (MSAL.NET) | Azure
description: 了解如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 提供你自己的 HttpClient 和代理来连接到 Azure AD。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: daae88cd8e76d0ae1af04c45a7191027e9adece9
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834940"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>使用 MSAL.NET 提供你自己的 HttpClient 和代理
[初始化公共客户端应用程序](msal-net-initializing-client-applications.md)时，可以使用 `.WithHttpClientFactory method` 提供你自己的 HttpClient。  提供你自己的 HttpClient 可以实现高级方案，例如对 HTTP 代理的精细控制、自定义用户代理标头，或者强制 MSAL 使用特定的 HttpClient（例如在 ASP.NET Core Web 应用/API 中）。

## <a name="initialize-with-httpclientfactory"></a>使用 HttpClientFactory 进行初始化
下面的示例展示了如何创建 `HttpClientFactory`，然后通过它来初始化公共客户端应用程序：

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient 和 Xamarin iOS
使用 Xamarin iOS 时，建议创建一个 `HttpClient` 并让其针对 iOS 7 和更高版本显式使用基于 `NSURLSession` 的处理程序。 MSAL.NET 会自动创建一个 `HttpClient`，它针对 iOS 7 和更高版本使用 `NSURLSessionHandler`。 有关详细信息，请阅读[适用于 HttpClient 的 Xamarin iOS 文档](/xamarin/cross-platform/macios/http-stack)。