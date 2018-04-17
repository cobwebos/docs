---
title: Azure Active Directory 中的 B2B 协作用户声明映射 | Microsoft 文档
description: 为 Azure Active Directory (Azure AD) B2B 用户自定义在 SAML 令牌中颁发的用户声明。
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/06/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 8f5e471d4e7102300cd5581976b45c9fa8cc57bc
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Azure Active Directory 中的 B2B 协作用户声明映射

Azure Active Directory (Azure AD) 支持为 B2B 协作用户自定义在 SAML 令牌中颁发的声明。 当用户向应用程序进行身份验证时，Azure AD 会将一个 SAML 令牌颁发给应用，其中包含用于唯一标识用户的用户相关信息（或声明）。 默认情况下，这些信息包括用户的用户名、电子邮件地址、名字和姓氏。

在 [Azure 门户](https://portal.azure.com)中，可以查看或编辑在 SAML 令牌中发送到应用程序的声明。 若要访问设置，请选择“Azure Active Directory” > “企业应用程序” > 配置了单一登录的应用程序 >“单一登录”。 在“用户属性”部分中查看 SAML 令牌设置。

![在 UI 中显示 SAML 令牌属性](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

有两个可能的原因导致你可能需要编辑在 SAML 令牌中颁发的声明：

1. 应用程序需要一组不同的声明 URI 或声明值。

2. 应用程序要求 NameIdentifier 声明是存储在 Azure AD 中的用户主体名称 (UPN) 以外的其他内容。

有关如何添加和编辑声明的信息，请参阅[在 Azure Active Directory 中为企业应用程序自定义在 SAML 令牌中颁发的声明](develop/active-directory-saml-claims-customization.md)。

对于 B2B 协作用户，由于安全原因，将阻止跨租户映射 NameID 和 UPN。

## <a name="next-steps"></a>后续步骤

- 有关 B2B 协作用户属性的信息，请参阅 [Azure Active Directory B2B 协作用户的属性](active-directory-b2b-user-properties.md)。
- 有关 B2B 协作用户的用户令牌的信息，请参阅[了解 Azure AD B2B 协作中的用户令牌](active-directory-b2b-user-token.md)。

