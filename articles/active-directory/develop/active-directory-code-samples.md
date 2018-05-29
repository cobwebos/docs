---
title: Azure Active Directory 代码示例 | Microsoft Docs
description: 提供 Azure Active Directory（v1 终结点）代码示例的索引，这些示例按方案进行组织。
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
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 299e0d4fa53f9b8a2aef2fc881b136aa41aacfe4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157769"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Azure Active Directory 代码示例（V1 终结点）

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

可以使用 Microsoft Azure Active Directory (Azure AD) 向 web 应用程序和 web API 添加身份验证和授权。

此部分提供可用于详细了解 Azure AD V1 终结点的示例链接。 这些示例将展示其工作原理以及可以在应用程序中使用的代码片段。 在代码示例页上，可以找到在要求、安装和设置方面提供帮助的详细自述主题。 并且代码带有注释，可以帮助你理解关键部分。

> [!NOTE]
> 如果对 Azure AD V2 代码示例感兴趣，请参阅[按方案分类的 v2.0 代码示例](active-directory-v2-code-samples.md)。

若要了解每种示例类型的基本方案，请参阅 [Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)。

你也可以在 GitHub 上为我们的示例做出补充。 若要了解如何操作，请参阅 [Microsoft Azure Active Directory 示例和文档](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>调用 Microsoft Graph 或 Web API 的桌面和移动公共客户端应用程序

以下示例展示了以用户身份访问 Microsoft Graph 或 Web API 的公共客户端应用程序（桌面/移动应用程序）。

客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph | 调用 ASP.NET 或 ASP.NET Core 2.0 Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
桌面 (WPF)           | .NET/C# | 交互 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
移动 (UWP)            | .NET/C#  | 交互 | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store)（单租户 Web API） </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store)（多租户 Web API）|
移动（Android、iOS、UWP）   | .NET/C# (Xamarin) | 交互 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
移动 (Android)           | Android/Java | 交互 |   [android](https://github.com/Azure-Samples/active-directory-android) |
移动 (iOS)           | iOS/Objective C | 交互 |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
桌面（控制台）          | .NET/C# | 用户名/密码 </p> Windows 集成身份验证 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
桌面（控制台）           | .NET Core/C# | 设备配置文件 | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Web 应用程序

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>可让用户登录、使用用户标识调用 Microsoft Graph 或 Web API 的 Web 应用程序

 平台 | 仅让用户登录 | 调用 Microsoft Graph 或 AAD Graph| 调用另一个 ASP.NET 或 ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>演示基于角色的访问控制（授权）的 Web 应用程序

以下示例展示了如何实施基于角色的访问控制，用于限制特定用户对 Web 应用程序特定功能的权限。 系统将根据用户属于 Azure AD 组还是角色对其授权。

平台 | 示例 | 说明
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | 使用 Azure AD **组**进行授权的 .NET 4.5 MVC Web 应用
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | 使用 Azure AD **角色**进行授权的 .NET 4.5 MVC Web 应用

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>守护程序应用程序（使用应用程序标识访问 Web API）

以下示例展示了可在无用户的情况下（使用应用程序标识）访问 Microsoft Graph 或 Web API 的桌面或 Web 应用程序。

客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph | 调用 ASP.NET 或 ASP.NET Core 2.0 Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
守护程序应用（控制台）          | .NET/C#  | 使用应用密码或证书的客户端凭据 | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
守护程序应用（控制台）         | .NET Core/C# | 使用证书的客户端凭据| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
桌面型            | Java | 客户端凭据 |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET Web 应用  | .NET/C# | 客户端凭据 |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>受 Azure Active Directory 保护的 Web API

以下示例展示了如何使用 Azure AD 保护 node.js Web API。

平台 | 示例 | 说明
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  使用 Azure AD 和 OAuth 2.0 访问令牌保护的 NodeJS Web API。

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>调用 Microsoft Graph 或另一个 Web API 的 Web API

以下示例展示了调用另一个 Web API 的 Web API。 第二个示例展示了如何处理条件访问。

 平台 |  调用 Microsoft Graph | 调用另一个 ASP.NET 或 ASP.NET Core 2.0 Web API
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>单页应用程序

此示例展示了如何编写受 Azure AD 保护的单页面应用程序。

 平台 |  调用 Microsoft Graph | 调用自身的 API
 -------- |  --------------------- | -------------------------
JavaScript (Angular) / ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 示例

有关演示 Microsoft Graph API 的各种使用模式（包括向 Azure AD 进行身份验证）的示例和教程，请参阅 [Microsoft Graph Community Samples & Tutorials](https://github.com/microsoftgraph/msgraph-community-samples)（Microsoft Graph 社区示例和教程）。

## <a name="see-also"></a>另请参阅

[Azure Active Directory 开发人员指南](active-directory-developers-guide.md)

[Azure AD 图形 API 概念和参考](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD 图形 API 帮助程序库](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
