---
title: 将多租户应用程序添加到 Azure AD 应用程序库 | Microsoft Docs
description: 说明如何在 Azure AD 应用程序库中列出自定义开发的多租户应用程序。
services: active-directory
documentationCenter: na
author: barbkess
manager: mtillman
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 2f112a191770a2b469f4f8d1bce25973999f40d6
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366864"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>将多租户应用程序添加到 Azure AD 应用程序库

## <a name="what-is-the-azure-ad-application-gallery"></a>什么是 Azure AD 应用程序库？

Azure Active Directory (Azure AD) 是一种基于云的标识服务。 
  [Azure AD 应用程序库](https://azure.microsoft.com/marketplace/active-directory/all/)位于 Azure 市场中，所有应用程序连接器都发布在其中以用于实现单一登录和用户预配。 使用 Azure AD 作为标识提供者的客户可以查找发布在这里的各种 SaaS 应用程序连接器。 IT 管理员从应用库中添加连接器，然后对连接器进行配置并将其用于单一登录和预配。 Azure AD 支持使用所有主要的联合身份验证协议（包括 SAML 2.0、OpenID Connect、OAuth 和 WS-Fed）进行单一登录。 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>如果应用程序支持 SAML 或 OpenIDConnect
如果想要将某个多租户应用程序在 Azure AD 应用程序库中列出，首先必须确保该应用程序支持以下单一登录技术之一：

- **OpenID Connect**：若要将应用列出，请在 Azure AD 中创建多租户应用程序并为应用程序实现 [Azure AD 许可框架](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework)。 将登录请求发送到公用终结点，以便任何客户都可以向应用程序提供许可。 可以根据在令牌中收到的租户 ID 和用户 UPN 来控制用户的访问。 使用[在 Azure Active Directory 应用程序库中列出你的应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)中所述的过程提交应用程序。

- **SAML**：如果应用程序支持 SAML 2.0，则可以在该库中列出。 按照[在 Azure Active Directory 应用程序库中列出你的应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)中的说明进行操作。

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>如果应用程序不支持 SAML 或 OpenIDConnect
不支持 SAML 或 OpenIDConnect 的应用程序仍可通过密码单一登录技术集成到应用程序库。

密码单一登录也称为密码保管，可用于管理用户对不支持联合身份验证的 Web 应用程序的访问权限和密码。 如果有多个用户需要共享单个帐户（例如共享组织的社交媒体应用帐户），此功能也很有用。 

如果想要使用此技术列出你的应用程序：
1. 创建具有 HTML 登录页的 Web 应用程序来配置[密码单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。 
2. 按照[在 Azure Active Directory 应用程序库中列出你的应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)中所述提交请求。

## <a name="escalations"></a>升级

若要进行升级，请向 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)发送电子邮件，我们会尽快进行回复。

## <a name="next-steps"></a>后续步骤
了解如何[在 Azure Active Directory 应用程序库中列出你的应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)。
