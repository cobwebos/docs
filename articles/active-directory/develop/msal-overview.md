---
title: 了解 MSAL | Azure
titleSuffix: Microsoft identity platform
description: 借助 Microsoft 身份验证库 (MSAL)，应用程序开发人员可以获取令牌，以调用受保护的 Web API。 这些 Web API 可以是 Microsoft Graph、其他 Microsoft API、第三方 Web API 或你自己的 Web API。 MSAL 支持多个应用程序体系结构和平台。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 8fbcacf4e3c26f117be4ab5b64b35f670435e6b6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092743"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Microsoft 身份验证库 (MSAL) 的概述
利用 microsoft 身份验证库 (MSAL) ，开发人员可从 Microsoft 标识平台终结点获取 [令牌](developer-glossary.md#security-token) ，以便对用户进行身份验证并访问受保护的 web api。 它可用于提供对 Microsoft Graph、其他 Microsoft Api、第三方 web Api 或你自己的 web API 的安全访问。 MSAL 支持多种不同的应用程序体系结构和平台，包括 .NET、JavaScript、Java、Python、Android 和 iOS。

MSAL 为你提供了许多获取令牌的方法，将一致的 API 用于许多平台。 使用 MSAL 具有以下好处：

* 无需直接在应用程序中对协议使用 OAuth 库或代码。
* 代表用户或代表应用程序获取令牌（如果适用于平台）。
* 维护令牌缓存，并在即将过期时为你刷新令牌。 你不需要自行处理令牌过期。
* 帮助你指定你希望应用程序登录的受众（你的组织、一系列组织、工作和学校以及 Microsoft 个人帐户、Azure AD B2C 的社交身份、主权云和国家云中的用户）。
* 可帮助你通过配置文件设置应用程序。
* 可显示可操作异常、日志和遥测，从而帮助你对应用进行故障排除。

## <a name="application-types-and-scenarios"></a>应用程序类型和方案
通过使用 MSAL，可从许多应用程序类型获取令牌：Web 应用程序、Web API、单页应用 (JavaScript)、移动和本机应用程序，以及守护程序和服务器端应用程序。

可以在许多应用程序方案中使用 MSAL，包含以下方案：

* [单页应用程序 (JavaScript)](scenario-spa-overview.md)
* [Web 应用登录用户](scenario-web-app-sign-user-overview.md)
* [Web 应用程序登录用户并代表用户调用 web API](scenario-web-app-call-api-overview.md)
* [保护 Web API，因此仅通过身份验证的用户可以访问它](scenario-protected-web-api-overview.md)
* [Web API 代表已登录用户调用其他下游 Web API](scenario-web-api-call-api-overview.md)
* [桌面应用程序代表已登录用户调用 Web API](scenario-desktop-overview.md)
* [代表以交互方式登录的用户调用 Web API 的移动应用程序](scenario-mobile-overview.md)。
* [桌面/服务守护程序应用程序代表自己调用 Web API](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>语言和框架

| 库 | 支持的平台和框架|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework、.NET Core、Xamarin Android、Xamarin iOS、通用 Windows 平台|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript 框架：例如 AngularJS、Ember.js 或 Durandal.js|
| [适用于 Android 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [适用于 iOS 和 macOS 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS 和 macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows、macOS、Linux|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows、macOS、Linux|

## <a name="differences-between-adal-and-msal"></a>ADAL 和 MSAL 之间的差异

Active Directory 身份验证库 (ADAL) 与适用于开发人员的 Azure AD (v1.0) 终结点集成，其中 MSAL 与 Microsoft 标识平台 (v2.0) 终结点集成。 V1.0 终结点支持工作帐户，但不支持个人帐户。 V2.0 终结点将 Microsoft 个人帐户和工作帐户统一到单个身份验证系统。 此外，借助 MSAL，还可以获取 Azure AD B2C 的身份验证。

有关更多具体信息，请参阅[从 ADAL.NET 迁移到 MSAL.NET](msal-net-migration.md) 和[从 ADAL.js 迁移到 MSAL.js](msal-compare-msal-js-and-adal-js.md)。
