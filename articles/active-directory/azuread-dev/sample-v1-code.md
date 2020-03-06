---
title: Azure Active Directory 1.0 版的代码示例 |Microsoft Docs
description: 提供 Azure Active Directory（v1.0 终结点）代码示例的索引，这些示例按方案进行组织。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: azuread-dev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9b4479436077dbad8cf2fb4dc40954194c138981
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377689"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory 代码示例（v1.0 终结点）

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

你可以使用 Microsoft Azure Active Directory (Azure AD) 向你的 web 应用程序和 web API 添加身份验证和授权。

此部分提供可用于详细了解 Azure AD v1.0 终结点的示例链接。 这些示例将展示其工作原理以及可以在应用程序中使用的代码片段。 在代码示例页上，可以找到在要求、安装和设置方面提供帮助的详细自述主题。 并且代码带有注释，可以帮助你理解关键部分。

> [!NOTE]
> 如果对 Azure AD V2 代码示例感兴趣，请参阅[按方案分类的 v2.0 代码示例](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

若要了解每种示例类型的基本方案，请参阅 [Azure AD 的身份验证方案](v1-authentication-scenarios.md)。

你也可以在 GitHub 上为我们的示例做出补充。 若要了解如何操作，请参阅 [Microsoft Azure Active Directory 示例和文档](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>单页应用程序

此示例展示了如何编写受 Azure AD 保护的单页应用程序。

 平台 | 调用自身的 API | 调用其他 Web API
 -------- |  --------------------- | ------------------ 
![此图显示了 JavaScript 徽标](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![此图显示了角度 JS 徽标](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Web 应用程序

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>可让用户登录、使用用户标识调用 Microsoft Graph 或 Web API 的 Web 应用程序

以下示例说明了 Web 应用程序签名用户。 其中一些应用程序还以已登录用户的名义调用 Microsoft Graph 或你自己的 Web API。

 平台 | 仅让用户登录 | 调用 Microsoft Graph | 调用另一个 ASP.NET 或 ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
![此图像显示 ASP.NET 徽标](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![此图像显示 ASP.NET 徽标](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
![此图像显示 Python 徽标](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![此图显示了 Java 日志](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![此图显示了 PHP 徽标](media/sample-v2-code/logo-php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>演示基于角色的访问控制（授权）的 Web 应用程序

以下示例演示如何实现基于角色的访问控制 (RBAC)。 RBAC 用于将 Web 应用中某些功能的权限限制为某些用户。 系统将根据用户是属于 **Azure AD 组**还是拥有一个给定的应用程序**角色**，对其进行授权。

平台 | 示例 |
 -------- | ------------------- |
![此图像显示 ASP.NET 徽标](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | 使用 Azure AD **角色**进行授权的 .NET 4.5 MVC Web 应用

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>调用 Microsoft Graph 或 Web API 的桌面和移动公共客户端应用程序

以下示例说明了公共客户端应用程序（桌面/pmobile 应用程序），这些应用程序访问 Microsoft Graph 或用户名称中的 Web API。 根据设备和平台，应用程序可以用不同方式（流/授权）让用户登录：

- 交互式
- 以静默方式（在 Windows 上集成 Windows 身份验证，或用户名/密码）
- 通过将交互式登录委托给另一个设备（在不提供 web 控件的设备上使用的设备代码流）

客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph | 调用 ASP.NET 或 ASP.NET Core 2.x Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
桌面 (WPF)           | ![此图显示 .NET/C#徽标](media/sample-v2-code/logo-net.png)  | 交互 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) 的一部分 | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
移动 (UWP)            | .![此图显示 .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | 交互 | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> 此示例使用 [WAM](/windows/uwp/security/web-account-manager)，而不是 [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store)（使用 ADAL.NET 调用单租户 Web API 的 UWP 应用程序） </p> [dotnet-webapi-multite nant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store)（使用 ADAL.NET 调用多租户 Web API 的 UWP 应用程序）|
移动（Android、iOS、UWP）   | ![此图显示 .NET/C# （Xamarin）](media/sample-v2-code/logo-xamarin.png) | 交互 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
移动 (Android)           | ![此图像显示 Android 徽标](media/sample-v2-code/logo-android.png) | 交互 |   [android](https://github.com/Azure-Samples/active-directory-android) |
移动 (iOS)           | ![此图显示了 iOS/目标 C 或 Swift](media/sample-v2-code/logo-ios.png) | 交互 |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
桌面（控制台）          | ![此图显示 .NET/C#徽标](media/sample-v2-code/logo-net.png) | 用户名/密码 </p>  集成 Windows 身份验证 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
桌面（控制台）          | ![此图显示了 Java 徽标](media/sample-v2-code/logo-java.png) | 用户名/密码 | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
桌面（控制台）           | ![此图显示 .NET Core/C#徽标](media/sample-v2-code/logo-netcore.png) | 设备代码流 | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>后台应用程序（将 web Api 与应用程序的标识进行访问）

以下示例展示了可在无用户的情况下（使用应用程序标识）访问 Microsoft Graph 或 Web API 的桌面或 Web 应用程序。

客户端应用程序 | 平台 | 流/授权 | 调用 ASP.NET 或 ASP.NET Core 2.0 Web API
------------------ | -------- | ---------- | -------------------- 
守护程序应用（控制台）          | ![此图显示 .NET 徽标](media/sample-v2-code/logo-netframework.png) | 使用应用密码或证书的客户端凭据 | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
守护程序应用（控制台）         | ![此图显示 .NET 徽标](media/sample-v2-code/logo-netcore.png) | 使用证书的客户端凭据| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET Web 应用  | ![此图显示 .NET 徽标](media/sample-v2-code/logo-netframework.png) | 客户端凭据 | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>受 Azure Active Directory 保护的 Web API

以下示例展示了如何使用 Azure AD 保护 node.js Web API。

在本文的前几部分中，还可以找到其他示例，这些示例演示了一个**调用** ASP.NET 或 ASP.NET Core **Web API** 的客户端应用程序。 本部分不再提及这些示例，但你可以在上表或下表的最后一列中找到它们

| 平台 | 示例 |
|--------|-------------------|
| ![此图像显示 node.js 徽标](media/sample-v2-code/logo-nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>调用 Microsoft Graph 或另一个 Web API 的 Web API

以下示例展示了调用另一个 Web API 的 Web API。 第二个示例演示如何处理条件性访问。

| 平台 |  调用 Microsoft Graph | 调用另一个 ASP.NET 或 ASP.NET Core 2.0 Web API |
| -------- |  --------------------- | ------------------------- |
| ![此图像显示 ASP.NET 徽标](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 示例

有关演示 Microsoft Graph API 的各种使用模式（包括向 Azure AD 进行身份验证）的示例和教程，请参阅 [Microsoft Graph Community Samples & Tutorials](https://github.com/microsoftgraph/msgraph-community-samples)（Microsoft Graph 社区示例和教程）。

## <a name="see-also"></a>另请参阅

- [Azure Active Directory 开发人员指南](v1-overview.md)
- [Azure Active Directory 身份验证库](active-directory-authentication-libraries.md)
- [Microsoft Graph API 概念和参考](https://docs.microsoft.com/graph/use-the-api)
