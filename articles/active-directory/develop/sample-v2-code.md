---
title: Microsoft 标识平台的代码示例
description: 提供了按方案组织的可用 Microsoft 标识平台（v2.0 终结点）代码示例的索引。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 713fa09319f1b95fe2bcc0d15c973a2096a250ec
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801140"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft 标识平台代码示例（v2.0 终结点）

可以使用 Microsoft 标识平台执行以下操作：

- 向 Web 应用程序和 Web API 添加身份验证和授权。
- 要求使用访问令牌来访问受保护的 Web API。

本文简要介绍了 Microsoft 标识平台终结点示例并提供了指向这些示例的链接。 这些示例将展示其工作原理，并提供可以在应用程序中使用的代码片段。 在代码示例页上，可以找到在要求、安装和设置方面提供帮助的详细自述主题。 代码中的注释可帮助你理解关键部分。

> [!NOTE]
> 如果对 v1.0 示例感兴趣，请参阅 [Azure AD 代码示例（v1.0 终结点）](../azuread-dev/sample-v1-code.md)。

若要了解每种示例类型的基本方案，请参阅 [Microsoft 标识平台终结点的应用类型](v2-app-types.md)。

你也可以为 GitHub 上的示例做出补充。 若要了解如何操作，请参阅 [Microsoft Azure Active Directory 示例和文档](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>单页应用程序

这些示例展示了如何编写由 Microsoft 标识平台保护的单页应用程序。 这些示例使用下列种类的 MSAL.js 之一。

| 平台 | 说明 | 链接 |
| -------- | --------------------- | -------- |
| ![此图显示了 JavaScript 徽标](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 调用 Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![此图显示了 JavaScript 徽标](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 使用 PKCE 的身份验证代码流调用 Microsoft Graph |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![此图显示了 JavaScript 徽标](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 调用 B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| 调用自己的 Web API | [ms-标识-javascript-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| 调用 Microsoft Graph  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![此图显示了响应徽标](media/sample-v2-code/logo_react.png) [JavaScript （msal）](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| 调用自己的 Web API，进而调用 Microsoft Graph  | [ms-identity-javascript-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | 调用 B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Web 应用程序

以下示例演示了将用户登录的 Web 应用。 一些示例还演示了使用用户标识调用 Microsoft Graph 或你自己的 Web API 的应用程序。

| Platform | 仅让用户登录 | 让用户登录并调用 Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core WebApp 让用户登录教程](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web 应用调用 Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) 阶段中的同一示例 |
| ![此图显示了 ASP.NET 徽标](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 快速入门](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![此图显示了 Ruby 徽标](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>桌面和移动公共客户端应用

以下示例展示了以用户身份访问 Microsoft Graph API 或你自己的 Web API 的公共客户端应用程序（桌面或移动应用程序）。 所有这些客户端应用程序均使用 Microsoft 身份验证库 (MSAL)。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph | 调用 ASP.NET Core web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 桌面 (WPF)      | ![此图显示了 .NET/C# 徽标](media/sample-v2-code/logo_NET.png) | [交互](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 桌面（控制台）   | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NET.png) | [Windows 集成身份验证](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 桌面（控制台）   | ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [Windows 集成身份验证](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌面（控制台）   | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NETcore.png) | [用户名/密码](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| 使用 WAM 的桌面（控制台）  | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NETcore.png) | [与 WAM 交互](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 桌面（控制台）   | ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [用户名/密码](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌面（控制台）   | ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png) | [用户名/密码](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| 移动（Android、iOS、UWP）   | ![此图显示了 .NET/C# (Xamarin) 徽标](media/sample-v2-code/logo_xamarin.png) | [交互](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| 移动 (iOS)       | ![此图显示了 iOS/Objective-C 或 Swift](media/sample-v2-code/logo_iOS.png) | [交互](msal-authentication-flows.md#interactive) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| 桌面 (macOS)       | macOS | [交互](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| 移动 (Android-Java)   | ![此图显示了 Android 徽标](media/sample-v2-code/logo_Android.png) | [交互](msal-authentication-flows.md#interactive) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 移动 (Android-Kotlin)   | ![此图显示了 Android 徽标](media/sample-v2-code/logo_Android.png) | [交互](msal-authentication-flows.md#interactive) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>守护程序应用程序

下面的示例展示了一个应用程序，它使用自己的标识（没有用户）访问 Microsoft Graph API。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| 控制台 | ![此图显示了 .NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [客户端凭据](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web 应用 | ![此图显示了 ASP.NET 徽标](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [客户端凭据](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| 控制台 | ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [客户端凭据](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 控制台 | ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png) | [客户端凭据](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>无外设应用程序

以下示例展示了在没有 Web 浏览器的设备上运行的公共客户端应用程序。 该应用可以是命令行工具，可以是在 Linux 或 Mac 上运行的应用，还可以是 IoT 应用程序。 此示例提供了一个应用，以用户身份访问 Microsoft Graph API，该用户以交互方式在另一台设备上登录（例如移动电话）。 此客户端应用程序使用 Microsoft 身份验证库 (MSAL)。

| 客户端应用程序 | Platform | 流/授权 | 调用 Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| 桌面（控制台）   | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NETcore.png) | [设备代码流](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 桌面（控制台）   | ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [设备代码流](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 桌面（控制台）   | ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png) | [设备代码流](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Web API

以下示例展示了如何使用 Microsoft 标识平台终结点保护 Web API，以及如何从 Web API 调用下游 API。

| 平台 | 示例 |
| -------- | ------------------- |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) 的 ASP.NET Core Web API（服务）  |
| ![此图显示了 ASP.NET 徽标](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) 的 Web API（服务） |
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) 的 Web API（服务） |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png) | [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) 的 Web API（服务） |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png) | [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 的 B2C Web API（服务） |

## <a name="azure-functions-as-web-apis"></a>Azure Functions 作为 Web API

以下示例演示如何使用 HttpTrigger 保护 Azure Functions 并通过 Microsoft 标识平台终结点公开 Web API，以及如何从 Web API 调用下游 API。

| 平台 | 示例 |
| -------- | ------------------- |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) 的 ASP.NET Core Web API（服务）Azure Functions  |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [NodeJS 和 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) 的 Web API（服务） |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) 的 Web API（服务） |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [使用代理流的 NodeJS 和 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) 的 Web API（服务） |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 示例

若要了解演示 Microsoft Graph API 的各种使用模式（包括向 Azure AD 进行身份验证）的[示例](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet)和教程，请参阅 [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples)（Microsoft Graph 社区示例和教程）。

## <a name="see-also"></a>另请参阅

- [Azure Active Directory (v1.0) 开发人员指南](../azuread-dev/v1-overview.md)
- [Microsoft Graph API 概念和参考](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
