---
title: "针对开发人员的 Azure Active Directory | Microsoft Docs"
description: "本文概述了如何使用 Azure Active Directory 登录 Microsoft 工作和学校帐户。"
services: active-directory
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: e1f9fbf6cb80065ea796e2d53d09f48fe57b207b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-active-directory-for-developers"></a>针对开发人员的 Azure Active Directory
Azure Active Directory (Azure AD) 是一项云标识服务，开发人员可以使用它将任何用户安全地登录到 Microsoft 工作或学校帐户。 本文档介绍了如何使用行业标准协议（OAuth2.0 与 OpenID Connect）向应用程序添加 Azure AD 支持。

| | |
| --- | --- |
|[身份验证基础知识](active-directory-authentication-scenarios.md) | 使用 Azure AD 进行身份验证简介 |
|[应用程序的类型](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD 支持的身份验证方案概述。 |                                
                                                                              
## <a name="get-started"></a>入门
下述指导性的设置演示了如何使用 Microsoft 身份验证库让 Azure AD 用户登录。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![移动和桌面应用](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />移动和桌面应用</center> | [概述](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Web 应用](./media/active-directory-developers-guide/Web_app.png)<br />Web 应用</center> | [概述](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |  |
| <center>![单页应用](./media/active-directory-developers-guide/SPA.png)<br />单页应用</center> | [概述](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [概述](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![服务到服务](./media/active-directory-developers-guide/Service_App.png)<br />服务到服务</center> | [概述](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>操作指南
以下指南介绍如何使用 Azure AD 执行常见任务。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[应用程序注册](active-directory-integrating-applications.md)           | 如何在 Azure AD 中注册应用程序。 |
|[多租户应用程序](active-directory-devhowto-multi-tenant-overview.md)    | 如何登录任何 Microsoft 工作帐户。 |
|[OAuth 和 OpenID Connect 协议](active-directory-protocols-openid-connect-code.md)| 如何使用 Microsoft 身份验证协议让用户登录并调用 Web API。 |
|[附加指南](active-directory-developers-guide-index.md#guides)        |  一系列适用于 Azure AD 的指南。   |

## <a name="reference-topics"></a>参考主题
以下文章详细介绍了在 Azure AD 中使用的 API、协议消息和术语。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [身份验证库 (ADAL)](active-directory-authentication-libraries.md)   | Azure AD 提供的库和 SDK 的概述。 |
| [代码示例](active-directory-code-samples.md)                                  | 所有 Azure AD 代码示例的列表。 |
| [术语表](active-directory-dev-glossary.md)                                      | 本文档通篇使用的术语和单词定义。 |
| [其他参考主题](active-directory-developers-guide-index.md#reference)| 一系列适用于 Azure AD 的参考主题。   |


> [!NOTE]
> 若需登录 Microsoft 个人帐户，可以考虑使用 [Azure AD v2.0 终结点](active-directory-appmodel-v2-overview.md)。 Azure AD v2.0 终结点将 Microsoft 个人帐户和 Microsoft 工作帐户（由 Azure AD 提供）统一成单个身份验证系统。


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
