---
title: "Azure Active Directory 中的 B2B 协作用户声明映射 | Microsoft 文档"
description: "Azure Active Directory B2B 协作的声明映射参考"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 439219087d0a5027c729e9d0e0ecb90bd2a9de3c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Azure Active Directory 中的 B2B 协作用户声明映射

Azure Active Directory (Azure AD) 支持自定义 B2B 协作用户的 SAML 令牌中颁发的声明。 当用户向应用程序进行身份验证时，Azure AD 会将一个 SAML 令牌颁发给应用，其中包含用于唯一标识用户的用户相关信息（或声明）。 默认情况下，这些信息包括用户的用户名、电子邮件地址、名字和姓氏。 可以在“属性”选项卡下查看或编辑通过 SAML 令牌发送到应用程序的声明。

有两个可能的原因使你可能需要编辑 SAML 令牌中颁发的声明。

1. 应用程序已编写为需要一组不同的声明 URI 或声明值

2. 应用程序要求 NameIdentifier 声明为存储在 Azure Active Directory 中的用户主体名称以外的其他内容。

  ![查看 SAML 令牌中的声明](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

有关如何添加和编辑声明的信息，请参阅[在 Azure Active Directory 中为预先集成的应用自定义 SAML 令牌中颁发的声明](develop/active-directory-saml-claims-customization.md)一文中声明自定义的相关内容。 对于 B2B 协作用户，由于安全原因，将阻止跨租户映射 NameID 和 UPN。


## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委派 B2bB 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作当前限制](active-directory-b2b-current-limitations.md)
