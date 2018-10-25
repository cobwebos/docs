---
title: Azure Active Directory 代码示例 | Microsoft Docs
description: 提供 Azure Active Directory（v1.0 终结点）代码示例的索引，这些示例按方案进行组织。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 46b66a6e718f18b76db6332487c350c2c199c342
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957768"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory 代码示例（v1.0 终结点）

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

可以使用 Microsoft Azure Active Directory (Azure AD) 向 web 应用程序和 web API 添加身份验证和授权。

此部分提供可用于详细了解 Azure AD v1.0 终结点的示例链接。 这些示例将展示其工作原理以及可以在应用程序中使用的代码片段。 在代码示例页上，可以找到在要求、安装和设置方面提供帮助的详细自述主题。 并且代码带有注释，可以帮助你理解关键部分。

> [!NOTE]
> 如果对 Azure AD V2 代码示例感兴趣，请参阅[按方案分类的 v2.0 代码示例](sample-v2-code.md)。

若要了解每种示例类型的基本方案，请参阅 [Azure AD 的身份验证方案](authentication-scenarios.md)。

你也可以在 GitHub 上为我们的示例做出补充。 若要了解如何操作，请参阅 [Microsoft Azure Active Directory 示例和文档](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>单页应用程序

此示例展示了如何编写受 Azure AD 保护的单页应用程序。

 平台 | 调用自身的 API | 调用其他 Web API
 -------- |  --------------------- | ------------------ | ----------------
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Web 应用程序

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>可让用户登录、使用用户标识调用 Microsoft Graph 或 Web API 的 Web 应用程序

以下示例说明了 Web 应用程序签名用户。 其中一些应用程序还以已登录用户的名义调用 Microsoft Graph 或你自己的 Web API。

 平台 | 仅让用户登录 | 调用 Microsoft Graph 或 AAD Graph| 调用另一个 ASP.NET 或 ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (AAD Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Php](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>演示基于角色的访问控制（授权）的 Web 应用程序

以下示例演示如何实现基于角色的访问控制 (RBAC)。 RBAC 用于将 Web 应用中某些功能的权限限制为某些用户。 系统将根据用户是属于 **Azure AD 组**还是拥有一个给定的应用程序**角色**，对其进行授权。

平台 | 示例
 -------- | -------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | 使用 Azure AD **角色**进行授权的 .NET 4.5 MVC Web 应用

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>调用 Microsoft Graph 或 Web API 的桌面和移动公共客户端应用程序

以下示例演示了以用户身份访问 Microsoft Graph 或 Web API 的公共客户端应用程序（桌面/移动应用程序）。 根据设备和平台，应用程序可以用不同方式（流/授权）让用户登录： 

- 交互方式、
- 无提示方式（使用 Windows 上集成的 Windows 身份验证或用户名/密码）， 
- 或者甚至通过将交互式登录委托给另一个设备（在不提供 Web 控件的设备上使用的设备代码流）。

客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph | 调用 ASP.NET 或 ASP.NET Core 2.x Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
桌面 (WPF)           | ![.NET/C#](media/sample-v2-code/logo_NET.png)  | 交互 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) 的一部分 | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
移动 (UWP)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | 交互 | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> 此示例使用 [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager)，而不是 [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store)（使用 ADAL.NET 调用单租户 Web API 的 UWP 应用程序） </p> [dotnet-webapi-multite nant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store)（使用 ADAL.NET 调用多租户 Web API 的 UWP 应用程序）|
移动（Android、iOS、UWP）   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | 交互 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
移动 (Android)           | ![Android / Java](media/sample-v2-code/logo_Android.png) | 交互 |   [android](https://github.com/Azure-Samples/active-directory-android) |
移动 (iOS)           | ![iOS / Objective C 或 swift](media/sample-v2-code/logo_iOS.png) | 交互 |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
桌面（控制台）          | ![.NET/C#](media/sample-v2-code/logo_NET.png) | 用户名/密码 </p>  Windows 集成身份验证 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
桌面（控制台）          | ![Java 控制台](media/sample-v2-code/logo_Java.png) | 用户名/密码 | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
桌面（控制台）           | ![.NET Core/C#](media/sample-v2-code/logo_NETcore.png) | 设备代码流 | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>守护程序应用程序（使用应用程序标识访问 Web API）

以下示例展示了可在无用户的情况下（使用应用程序标识）访问 Microsoft Graph 或 Web API 的桌面或 Web 应用程序。

客户端应用程序 | 平台 | 流/授权 | 调用 ASP.NET 或 ASP.NET Core 2.0 Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
守护程序应用（控制台）          | ![.NET](media/sample-v2-code/logo_NETframework.png) | 使用应用密码或证书的客户端凭据 | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
守护程序应用（控制台）         | ![.NET](media/sample-v2-code/logo_NETcore.png) | 使用证书的客户端凭据| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET Web 应用  | ![.NET](media/sample-v2-code/logo_NETframework.png) | 客户端凭据 | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>受 Azure Active Directory 保护的 Web API

以下示例展示了如何使用 Azure AD 保护 node.js Web API。

在本文的前几部分中，还可以找到其他示例，这些示例演示了一个**调用** ASP.NET 或 ASP.NET Core **Web API** 的客户端应用程序。 本部分不再提及这些示例，但你可以在上表或下表的最后一列中找到它们

平台 | 示例
 -------- | -------------------
![Php](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>调用 Microsoft Graph 或另一个 Web API 的 Web API

以下示例展示了调用另一个 Web API 的 Web API。 第二个示例展示了如何处理条件访问。

 平台 |  调用 Microsoft Graph | 调用另一个 ASP.NET 或 ASP.NET Core 2.0 Web API
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 示例

有关演示 Microsoft Graph API 的各种使用模式（包括向 Azure AD 进行身份验证）的示例和教程，请参阅 [Microsoft Graph Community Samples & Tutorials](https://github.com/microsoftgraph/msgraph-community-samples)（Microsoft Graph 社区示例和教程）。

## <a name="see-also"></a>另请参阅

[Azure Active Directory 开发人员指南](v1-overview.md)

[Azure Active Directory 身份验证库](active-directory-authentication-libraries.md)

[Azure AD 图形 API 概念和参考](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD 图形 API 帮助程序库](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
