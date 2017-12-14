---
title: "如何将多租户应用程序添加到 Azure AD 应用程序库 | Microsoft Docs"
description: "说明如何在 Azure AD 应用程序库中列出自定义开发的多租户应用程序"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 969273d1bc0750685a0b2010670915f4183ac8ed
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>如何将多租户应用程序添加到 Azure AD 应用程序库

## <a name="what-is-the-azure-ad-application-gallery"></a>什么是 Azure AD 应用程序库？

Azure AD 应用程序库是一种很好的方法，可以将应用程序呈现在数以百万计的 Azure Active Directory 客户面前，从而扩大了应用程序在应用商店中的影响和市场宣传。 以下步骤说明如何在 Azure AD 应用程序库中列出应用程序。

## <a name="if-your-application-supports-saml-or-openidconnect"></a>如果应用程序支持 SAML 或 OpenIDConnect
如果某个多租户应用程序要在 Azure AD 应用程序库中列出，首先必须确保应用程序支持以下单一登录技术之一：

1. **OpenID Connect** - 与 Azure AD 直接集成，使用 OpenID Connect 进行身份验证，并使用 Azure AD 许可 API 进行配置。 如果刚刚开始集成并且应用程序不支持 SAML，则这是建议的模式。
2. **SAML** – 应用程序已经能够使用 SAML 协议配置第三方标识提供者。

如果应用程序支持以下单一登录模式之一，并且想要列出 Azure AD 应用程序库中的多租户应用程序，可以按照以下文档中的步骤操作。 若要快速入门，请将电子邮件发送到 **waadpartners@microsoft.com**。

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>如果应用程序不支持 SAML 或 OpenIDConnect
即使应用程序不支持以下模式之一，我们仍可使用密码单一登录技术将其集成到库。 如果要浏览此选项，可以将电子邮件发送到 **waadpartners@microsoft.com**。

## <a name="next-steps"></a>后续步骤
[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
