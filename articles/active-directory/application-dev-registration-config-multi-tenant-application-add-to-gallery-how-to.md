---
title: "如何将多租户应用程序添加到 Azure AD 应用程序库 | Microsoft Docs"
description: "说明如何在 Azure AD 应用程序库中列出自定义开发的多租户应用程序"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>如何将多租户应用程序添加到 Azure AD 应用程序库

## <a name="what-is-the-azure-ad-application-gallery"></a>什么是 Azure AD 应用程序库？

Azure AD 是一种基于云的标识服务。 [Azure AD 应用库](https://azure.microsoft.com/marketplace/active-directory/all/)是一个公用存储，所有应用程序连接器都发布在其中以用于实现单一登录和用户预配。 我们的使用 Azure AD 作为标识提供者的共同客户将查找其中发布的各种 SaaS 应用程序连接器。 IT 管理员从应用库中添加连接器，对其进行配置并将其用于单一登录和预配。 Azure AD 支持使用所有主要的联合身份验证协议（例如 SAML 2.0、OpenID Connect、OAuth 和 WS-Fed）进行单一登录。 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>如果应用程序支持 SAML 或 OpenIDConnect
如果某个多租户应用程序要在 Azure AD 应用程序库中列出，首先必须确保应用程序支持以下单一登录技术之一：

1. **OpenID Connect** - 在 Azure AD 中创建多租户应用程序并为应用程序实现 [Azure AD 许可框架](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework)。 将登录请求发送到公用终结点，以便任何客户都可以向应用程序提供许可。 你可以根据在令牌中收到的租户 ID 和用户 UPN 控制客户用户访问。 请根据[此文章](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)中所述提交应用程序。

2. **SAML** – 如果应用程序支持 SAML 2.0，则可以将应用程序列在库中，[此处](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)列出了说明

如果应用程序支持以下单一登录模式之一，并且想要列出 Azure AD 应用程序库中的多租户应用程序，可以执行[此文章](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)中所述的步骤。 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>如果应用程序不支持 SAML 或 OpenIDConnect
即使应用程序不支持以下模式之一，我们仍可使用密码单一登录技术将其集成到库。

**密码 SSO** – 创建具有 HTML 登录页面的 Web 应用程序来配置[基于密码的单一登录](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis)。 基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 如果有多个用户需要共享单个帐户（例如共享组织的社交媒体应用帐户），此功能也很有用。 

如果希望使用此技术列出应用程序，请根据[此文章](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)中所述提交请求。

## <a name="escalations"></a>升级

若要进行升级，请向 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)发送电子邮件，我们会尽快回复。

## <a name="next-steps"></a>后续步骤
[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
