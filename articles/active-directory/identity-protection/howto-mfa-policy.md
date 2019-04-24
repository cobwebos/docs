---
title: 如何在 Azure Active Directory Identity Protection 中配置多重身份验证注册策略| Microsoft Docs
description: 了解如何配置 Azure AD Identity Protection 多重身份验证注册策略。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd1049551d7dbc4823636dfdc00f64afab72cdf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294824"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>如何：配置多重身份验证注册策略

Azure AD Identity Protection 可帮助你管理通过配置一个策略需要 MFA 注册多重身份验证 (MFA) 注册的推出。 本文介绍策略可以用途以及如何对其进行配置。

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>什么是多重身份验证注册策略？

Azure 多重身份验证是要求使用多种方式（而不仅仅是用户名和密码）对身份进行验证的一种方法。 它为用户登录和事务提供了附加的安全层。 为了使用户能够对 MFA 提示做出响应，它们必须先注册 mfa。 

建议要求对用户登录执行 Azure 多重身份验证，因为这种身份验证方法可以：

- 提供强式身份验证和一系列简单的验证选项

- 在帮助组织保护帐户以及在帐户遭到入侵后进行恢复方面发挥关键的作用


有关 MFA 的详细信息，请参阅[什么是 Azure 多重身份验证？](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>如何访问 MFA 注册策略？
   
MFA 注册策略位于 [Azure AD Identity Protection 页面](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)上的“配置”部分中。
   
![MFA 策略](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>策略设置

配置 MFA 注册策略时，需要设置：

- 该策略应用到的用户和组：

    ![用户和组](./media/howto-mfa-policy/11.png)

- 强制执行要求的 MFA 注册所需的控件：  

    ![访问](./media/howto-mfa-policy/12.png)

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
