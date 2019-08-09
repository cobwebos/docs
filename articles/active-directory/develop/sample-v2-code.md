---
title: Microsoft 标识平台的代码示例 |Microsoft Docs
description: 提供按方案组织的可用 Microsoft 标识平台 (v2.0 终结点) 代码示例的索引。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: bded69a256a6fb76eabcdc53bc4115f534cfda37
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852929"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft 标识平台代码示例（v2.0 终结点）

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

可以使用 Microsoft 标识平台执行以下操作：

- 向 Web 应用程序和 Web API 添加身份验证和授权。
- 要求使用访问令牌来访问受保护的 Web API。

本文简要介绍了 Microsoft 标识平台终结点示例并提供了指向这些示例的链接。 这些示例演示了它的完成方式, 还提供了可在应用程序中使用的代码片段。 在 "代码示例" 页上, 可以找到有助于满足要求、安装和设置的详细自述主题。 代码中的注释有助于理解关键部分。

> [!NOTE]
> 如果对 v1.0 示例感兴趣, 请参阅[Azure AD 代码示例 (1.0 版终结点)](sample-v1-code.md)。

若要了解每种示例类型的基本方案，请参阅 [Microsoft 标识平台终结点的应用类型](v2-app-types.md)。

你也可以为 GitHub 上的示例做出补充。 若要了解如何操作，请参阅 [Microsoft Azure Active Directory 示例和文档](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>单页应用程序

这些示例展示了如何编写由 Microsoft 标识平台保护的单页应用程序。 这些示例使用 MSAL 的一种风格。

| 平台 | 描述 | 链接 |
| -------- | --------------------- | -------- |
| ![此图像显示 javascript 徽标](media/sample-v2-code/logo_js.png) [javascript (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 调用 Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![此图像显示 javascript 徽标](media/sample-v2-code/logo_js.png) [javascript (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 调用 B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![此图像显示 javascript 徽标](media/sample-v2-code/logo_js.png) [javascript (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 调用自己的 Web API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![此图显示了角度 JS 徽标](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| 调用 Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![此图像显示角度徽标](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL 角)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| 调用 Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Web 应用程序

以下示例演示了将用户登录的 Web 应用。 一些示例还演示了使用用户标识调用 Microsoft Graph 或你自己的 Web API 的应用程序。

| 平台 | 仅让用户登录 | 让用户登录并调用 Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2。2 | [ASP.NET Core WebApp 让用户登录教程](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web 应用调用 Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) 阶段中的同一示例 |
| ![此图像显示 ASP.NET 徽标](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 快速入门](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![此图像显示 node.js 徽标](media/sample-v2-code/logo_nodejs.png)  |                   | [Node.js 快速入门](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![此图显示了 Ruby 徽标](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>桌面和移动公共客户端应用

以下示例显示了公共客户端应用程序 (桌面或移动应用程序), 这些应用程序访问 Microsoft Graph API, 或用户的名称中的 web API。 所有这些客户端应用程序都使用 Microsoft 身份验证库 (MSAL)。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph | 调用 ASP.NET Core 2.0 web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 桌面 (WPF)      | ![此图显示 .NET/C#徽标](media/sample-v2-code/logo_NET.png) | [交互](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 桌面（控制台）   | ![此图显示 .NET/C# (桌面) 徽标](media/sample-v2-code/logo_NET.png) | [Windows 集成身份验证](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 桌面（控制台）   | ![此图显示 .NET/C# (桌面) 徽标](media/sample-v2-code/logo_NETcore.png) | [用户名/密码](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| 移动（Android、iOS、UWP）   | ![此图显示 .NET/C# (Xamarin) 徽标](media/sample-v2-code/logo_xamarin.png) | [交互](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| 移动 (iOS)       | ![此图显示了 iOS/目标-C 或 Swift](media/sample-v2-code/logo_iOS.png) | [交互](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| 移动 (Android)   | ![此图像显示 Android 徽标](media/sample-v2-code/logo_Android.png) | [交互](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>守护程序应用程序

下面的示例展示了一个应用程序，它使用自己的标识（没有用户）访问 Microsoft Graph API。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| 控制台 | ![此图显示 .NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [客户端凭据](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web 应用 | ![此图像显示 ASP.NET 徽标](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [客户端凭据](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>无外设应用程序

以下示例展示了在没有 Web 浏览器的设备上运行的公共客户端应用程序。 应用可以是命令行工具, 也可以是在 Linux 或 Mac 上运行的应用, 也可以是 IoT 应用程序。 该示例在访问其他设备 (例如移动电话) 上以交互方式登录的用户的名称中提供了访问 Microsoft Graph API 的应用程序的功能。 此客户端应用程序使用 Microsoft 身份验证库 (MSAL)。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| 桌面（控制台）   | ![此图显示 .NET/C# (桌面) 徽标](media/sample-v2-code/logo_NETcore.png) | [设备代码流](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Web API

下面的示例演示如何使用 Microsoft 标识平台终结点保护 web API, 以及如何从 web API 调用下游 API。

| 平台 | 样本 |
| -------- | ------------------- |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2。2 | ASP.NET Core web API (服务) [dotnet-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![此图像显示 ASP.NET 徽标](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [Ms identity-webapi](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof)的 Web API (服务) |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 示例

若要了解演示 Microsoft Graph API 的各种使用模式（包括向 Azure AD 进行身份验证）的[示例](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet)和教程，请参阅 [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples)（Microsoft Graph 社区示例和教程）。

## <a name="see-also"></a>请参阅

- [Azure Active Directory (v1.0) 开发人员指南](v1-overview.md)
- [Azure AD 图形 API 概念和参考](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD 图形 API 帮助程序库](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
