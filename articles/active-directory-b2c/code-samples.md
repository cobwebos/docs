---
title: Azure Active Directory B2C 代码示例 | Microsoft Docs
description: Azure Active Directory B2C 移动、桌面、Web 和单页应用程序的代码示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 76cacbea3f98f65e290cb2722062127d2264e171
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91664828"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C 代码示例

以下各表提供了应用程序示例的链接，包括 iOS、Android、.NET 和 Node.js。

## <a name="mobile-and-desktop-apps"></a>移动和桌面应用

| 示例 | 说明 |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | 用 Swift 编写的一个 iOS 示例，它使用 OAuth 2.0 对 Azure AD B2C 用户进行身份验证并调用一个 API |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | 一个简单的 Android 应用，它展示了如何使用 MSAL 来通过 Azure Active Directory B2C 对用户进行身份验证，并使用生成的令牌访问一个 Web API。 |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | 此示例展示了如何利用第三方库来使用 Objective-C 开发 iOS 应用程序，用以通过我们的 Azure AD B2C 标识服务对 Microsoft 标识用户进行身份验证。 |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | 此示例展示了如何利用第三方库来开发 Android 应用程序，用以通过我们的 B2C 标识服务对 Microsoft 标识用户进行身份验证并使用 OAuth 2.0 访问令牌调用一个 Web API。 |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | 此示例展示了 Windows Desktop .NET (WPF) 应用程序如何使用 Azure AD B2C 完成用户登录，使用 MSAL.NET 获取访问令牌，并调用一个 API。 |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | 一个简单的 Xamarin Forms 应用，它展示了如何使用 MSAL 来通过 Azure Active Directory B2C 对用户进行身份验证，并使用生成的令牌访问一个 Web API。 |

## <a name="web-apps-and-apis"></a>Web 应用和 API

| 示例 | 说明 |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | 调用了一个 .NET Web API 的 .NET Web 应用程序的组合示例，两者都使用 Azure AD B2C 提供保护。 |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | 使用 OpenID Connect 在 Azure AD B2C 中登录用户的 ASP.NET Core Web 应用程序。 |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | 一个 ASP.NET Core Web 应用程序，它可以使用 Azure AD B2C 完成用户登录，使用 MSAL.NET 获取访问令牌，并调用一个 API。 |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | 一个 Node.js 应用，它提供了一种便捷的方法来使用 OpenID Connect 设置通过 Express 开发的 Web 应用程序。 |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | 一个用于 Azure AD B2C 的小 node.js Web API，它展示了如何使用 passport.js 保护 Web API 并接受 B2C 访问令牌。 |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | 演示如何将 Microsoft 标识平台的 B2C 与 Python Web 应用程序集成。  |

## <a name="single-page-apps"></a>单页应用

| 示例 | 说明 |
|--------| ----------- |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | 调用了一个 Web API 的单页应用程序 (SPA)。 通过 MSAL.js 使用 Azure AD B2C 完成身份验证。 |

## <a name="saml-test-application"></a>SAML 测试应用程序

| 示例 | 说明 |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | SAML 测试应用程序，用于测试已配置为充当 SAML 标识提供者的 Azure AD B2C。 |
