---
title: "针对开发人员的 Azure Active Directory | Microsoft Docs"
description: "本文概述了如何使用 Azure Active Directory 登录 Microsoft 工作和学校帐户。"
services: active-directory
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: bryanla
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: c7f758900ca89ed8bf08090cda5964eccc876e1d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017

---
# <a name="azure-active-directory-for-developers"></a>针对开发人员的 Azure Active Directory
Azure Active Directory 是一项云标识服务，开发人员可以使用它将任何用户登录到 Microsoft 支持的工作或学校帐户。  本文档介绍了如何使用行业的标准身份验证协议、OAuth 与 OpenID Connect 向应用程序添加 Azure AD 支持。

| | |
| --- | --- |
|[身份验证基础知识](active-directory-authentication-scenarios.md) | 使用 Azure AD 进行身份验证简介 |
|[应用程序的类型](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD 支持的身份验证方案概述 |                                
                                                                              
## <a name="get-started"></a>入门
这些指导性的设置演示了如何使用身份验证库让 Azure Active Directory 用户登录。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![移动应用和桌面应用](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />移动应用和桌面应用</center> | [概述](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Web 应用](./media/active-directory-developers-guide/Web_app.png)<br />Web 应用</center> | [概述](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![单页应用](./media/active-directory-developers-guide/SPA.png)<br />单页应用</center> | [概述](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript]((https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [概述](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![服务到服务](./media/active-directory-developers-guide/Service_App.png)<br />服务到服务</center> | [概述](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[OAuth 2.0 客户端凭据](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>指南
这些文章介绍如何使用 Azure Active Directory 执行常见任务。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[应用注册](active-directory-integrating-applications.md)           | 如何在 Azure AD 中注册应用 |
|[多租户应用](active-directory-devhowto-multi-tenant-overview.md)    | 如何登录任何 Microsoft 工作帐户 |
|[OAuth 与 OpenID Connect](active-directory-protocols-openid-connect-code.md)| 如何使用我们的新式身份验证协议使用户登录并调用 Web API |
|[更多指南...](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>引用
这些文章详细介绍了在 Azure Active Directory 中使用的 API、协议消息和术语。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [身份验证库 (ADAL)](active-directory-authentication-libraries.md)   | Azure AD 提供的库和 SDK 的概述 |
| [代码示例](active-directory-code-samples.md)                                  | 所有 Azure AD 代码示例的列表 |
| [术语表](active-directory-dev-glossary.md)                                      | 本文档通篇使用的术语和单词的定义 |
| [更多参考资料...](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>帮助和支持
可以通过这些地方获取在 Azure Active Directory 上进行开发的帮助。

|  |  
|---|
|[Stack Overflow 的 `azure-active-directory` 和 `adal` 标记](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[有关 Azure Active Directory 的反馈](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|

<br />

> [!NOTE]
> 若需登录 Microsoft 个人帐户，可以考虑使用 [Azure AD v2.0 终结点](active-directory-appmodel-v2-overview.md)。  Azure AD v2.0 终结点将 Microsoft 个人帐户和 Microsoft 工作帐户（由 Azure AD 提供）统一成单个身份验证系统。

