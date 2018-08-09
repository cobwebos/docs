---
title: 针对开发人员的 Azure Active Directory | Microsoft Docs
description: 本文概述了如何使用 Azure Active Directory 登录 Microsoft 工作和学校帐户。
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 666a677943811af05cd3403eab4887271c1f87b3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591204"
---
# <a name="azure-active-directory-for-developers"></a>针对开发人员的 Azure Active Directory

Azure Active Directory (Azure AD) 是一个云标识服务，开发人员可以使用它来生成应用，让用户使用 Microsoft 工作或学校帐户安全登录。 Azure AD 支持开发人员生成单租户业务线 (LOB) 应用和多租户应用。 除了基本登录以外，Azure AD 还可以让应用调用 [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) 等 Microsoft API，以及在 Azure AD 平台上生成的自定义 API。 本文档介绍了如何使用行业标准协议（例如 OAuth2.0 与 OpenID Connect）向应用程序添加 Azure AD 支持。

> [!NOTE]
> 本页面中的大部分内容侧重于仅支持 Microsoft 工作或学校帐户的 Azure AD v1.0 终结点。 若要通过使用者或个人 Microsoft 帐户登录，请参阅有关 [Azure AD v2.0 终结点](active-directory-appmodel-v2-overview.md)的信息。 对于想要让用户使用 Azure AD 帐户（工作和学校）和个人 Microsoft 帐户登录的应用，Azure AD v2.0 终结点提供统一的开发人员体验。

| | |
| --- | --- |
|[身份验证基础知识](authentication-scenarios.md) | 使用 Azure AD 进行身份验证简介 |
|[应用程序的类型](authentication-scenarios.md#application-types-and-scenarios) | Azure AD 支持的身份验证方案概述。 |      
| | |

## <a name="get-started"></a>入门
以下指导性设置逐步讲解如何使用 Azure AD 身份验证库 (ADAL) SDK 在偏好的平台上生成应用。 如需 Microsoft 身份验证库 (MSAL) 的用法信息，请参阅有关 [Azure AD v2.0 终结点](active-directory-appmodel-v2-overview.md)的文档。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![移动和桌面应用](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />移动和桌面应用</center> | [概述](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Web 应用](./media/azure-ad-developers-guide/Web_app.png)<br />Web 应用</center> | [概述](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](quickstart-v1-openid-connect-code.md) |
| <center>![单页应用](./media/azure-ad-developers-guide/SPA.png)<br />单页应用</center> | [概述](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![Web API](./media/azure-ad-developers-guide/Web_API.png)<br />Web API</center> | [概述](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.js](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![服务到服务](./media/azure-ad-developers-guide/Service_App.png)<br />服务到服务</center> | [概述](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>操作指南
以下指导逐步讲解如何在 Azure AD 中完成一些最常见的任务。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[应用程序注册](quickstart-v1-integrate-apps-with-azure-ad.md)           | 如何在 Azure AD 中注册应用程序。 |
|[多租户应用程序](howto-convert-app-to-be-multi-tenant.md)    | 如何登录任何 Microsoft 工作帐户。 |
|[OAuth 和 OpenID Connect 协议](v1-protocols-openid-connect-code.md)| 如何使用 Microsoft 身份验证协议让用户登录并调用 Web API。 |
|  |  |

## <a name="reference-topics"></a>参考主题
以下文章详细介绍了在 Azure AD 中使用的 API、协议消息和术语。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [身份验证库 (ADAL)](active-directory-authentication-libraries.md)   | Azure AD 提供的库和 SDK 的概述。 |
| [代码示例](sample-v1-code.md)                                  | 所有 Azure AD 代码示例的列表。 |
| [术语表](developer-glossary.md)                                      | 本文档通篇使用的术语和单词定义。 |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
