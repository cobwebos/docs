---
title: 如何在 Azure Active Directory Identity Protection 中配置多重身份验证注册策略| Microsoft Docs
description: 了解如何配置 Azure AD Identity Protection 多重身份验证注册策略。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: ed1104d3e5ddf54657fb1f7b5c84a85ea855d95c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55148571"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>如何：配置多重身份验证注册策略

Azure AD Identity Protection 可以通过配置一个策略来帮助管理多重身份验证 (MFA) 注册的首次实施。 本文介绍了可以使用该策略来做什么以及如何配置它。

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>什么是多重身份验证注册策略？

Azure 多重身份验证是要求使用多种方式（而不仅仅是用户名和密码）对身份进行验证的一种方法。 它为用户登录和事务提供了附加的安全层。  

建议要求对用户登录执行 Azure 多重身份验证，因为这种身份验证方法可以：

- 提供强式身份验证和一系列简单的验证选项

- 在帮助组织保护帐户以及在帐户遭到入侵后进行恢复方面发挥关键的作用


有关更多详细信息，请参阅[什么是 Azure 多重身份验证？](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>如何访问 MFA 注册策略？
   
MFA 注册策略位于 [Azure AD Identity Protection 页面](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)上的“配置”部分中。
   
![MFA 策略](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>策略设置

配置登录风险策略时，需要设置：

- 该策略应用到的用户和组：

    ![用户和组](./media/howto-mfa-policy/11.png)

- 要强制实施的访问类型：  

    ![用户和组](./media/howto-mfa-policy/12.png)

- 策略的状态：

    ![强制实施策略](./media/howto-mfa-policy/14.png)


策略配置对话框提供了一个选项用于评估配置的影响。

![估计的影响](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>用户体验


如需相关用户体验的概述，请参阅：

* [多重身份验证注册流程](flows.md#multi-factor-authentication-registration)。  
* [Azure AD Identity Protection 中的登录体验](flows.md)。  



## <a name="next-steps"></a>后续步骤

若要获取 Azure AD Identity Protection 的概述，请参阅 [Azure AD Identity Protection 概述](overview.md)。
