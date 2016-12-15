---
title: "如何使 AppSource 通过 Azure Active Directory 的认证 | Microsoft Docs"
description: "详细说明如何使应用程序 AppSource 通过 Azure Active Directory 的认证。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2016
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f34c5c10ec57f46fc9179362ddb77fce41bccf14


---
# <a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>如何使 AppSource 通过 Azure Active Directory (AD) 的认证
若要使 AppSource 通过 Azure AD 的认证，你的应用程序必须使用 OpenID Connect、OAuth 2.0 或 SAML 2.0 协议在 Azure AD 中实现多租户登录模式。 

如果你不熟悉 Azure AD 登录或多租户应用程序开发：

1. 请先阅读[Azure AD 身份验证方案中的浏览器到 Web 应用场景][AAD-Auth-Scenarios-Browser-To-WebApp]。  
2. 接下来，请阅读 Azure AD [Web 应用程序快速入门指南][AAD-QuickStart-Web-Apps]，其中演示了如何实现登录，并随附了代码示例。 
   
   > [!TIP]
   > 欢迎试用新的[开发人员门户](https://identity.microsoft.com/Docs/Web)预览版，只需花费几分钟时间，它就能帮助你开始使用 Azure Active Directory！  在开发人员门户中，可以逐步完成注册应用并将 Azure AD 集成到代码的整个过程。  完成上述过程后，将会创建一个可对租户中的用户进行身份验证的简单应用程序，以及一个可以接受令牌和执行验证的后端。
   > 
   > 
3. 若要了解如何在 Azure AD 中实现多租户登录模式，请查看[如何使用多租户应用程序模式将使任何 Azure Active Directory (AD) 用户登录][AAD-Howto-Multitenant-Overview]。

## <a name="related-content"></a>相关内容
有关构建支持 Azure AD 登录的应用程序的详细信息，或要获得帮助和支持，请参阅 [Azure AD 开发人员指南][AAD-Dev-Guide]。

阅读完本文后，请使用 Disqus 意见部分提供反馈，帮助我们改进内容。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->













<!--HONumber=Nov16_HO3-->


