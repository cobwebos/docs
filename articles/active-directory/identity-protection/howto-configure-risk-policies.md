---
title: 如何在 Azure Active Directory Identity Protection（已刷新）中配置风险策略 | Microsoft Docs
description: 如何在 Azure Active Directory Identity Protection（已刷新）中配置风险策略。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc2a1bd8b751ab1b88e54876227892ea3c257a3
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517532"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>如何：在 Azure Active Directory Identity Protection（已刷新）中配置风险策略


Azure AD 可以检测指示标识可能遭到入侵的风险事件。 通过配置风险策略，可以定义对检测结果的自动响应：

- 使用登录风险策略，可以配置对用户登录期间检测到的实时风险事件的响应。 
- 使用用户风险策略，可以配置响应，以应对在一段时间内为用户检测到的所有活动的用户风险。  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]


## <a name="what-is-the-sign-in-risk-policy"></a>什么是登录风险策略？

Azure AD 会分析用户的每次登录。 分析的目的是检测伴随登录而来的可疑操作。 例如，登录是否是使用匿名 IP 地址执行的？或者，登录是否是从不熟悉的位置发起的？ 在 Azure AD 中，系统可以检测到的可疑操作也称为风险事件。 根据在登录期间检测到的风险事件，Azure AD 会计算一个值。 该值表示登录不是由合法用户执行的可能性（低、中、高）。 此可能性称为**登录风险级别**。

登录风险策略是可以为特定登录风险级别配置的自动响应。 在响应中，你可以阻止对资源的访问，或者要求传递多重身份验证 (MFA) 质询来获取访问权限。

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>如何访问登录风险策略？
   
登录风险策略位于 [Azure AD Identity Protection 页](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)上的“配置”部分中。
   
![登录风险策略](./media/howto-configure-risk-policies/1014.png "登录风险策略")


## <a name="sign-in-risk-policy-settings"></a>登录风险策略设置

配置登录风险策略时，需要设置：

- 该策略应用到的用户和组：

    ![用户和组](./media/howto-configure-risk-policies/11.png)

- 触发该策略的登录风险级别：

    ![登录风险级别](./media/howto-configure-risk-policies/12.png)

- 当满足登录风险级别时要强制实施的访问类型：  

    ![访问](./media/howto-configure-risk-policies/13.png)

- 策略的状态：

    ![强制实施策略](./media/howto-configure-risk-policies/14.png)


策略配置对话框提供了一个选项来评估重新配置的影响。

![估计影响](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>登录风险策略要点

可以配置登录风险安全策略来要求进行 MFA：

![要求 MFA](./media/howto-configure-risk-policies/16.png)

但是，出于安全原因，此设置仅适用于已注册 MFA 的用户。 如果用户尚未注册 MFA，则标识保护会使用 MFA 要求阻止用户。

如果希望要求有风险的登录进行 MFA，则应当：

1. 对受影响的用户启用多重身份验证注册策略。

2. 要求受影响的用户在无风险会话中登录以执行 MFA 注册。

完成这些步骤可确保对有风险的登录要求执行多重身份验证。

登录风险策略：

- 应用到所有使用新式身份验证的浏览器流量和登录。

- 不会应用到使用旧式安全协议并在联合 IDP 上禁用 WS-Trust 终结点的应用程序，例如 ADFS。


如需相关用户体验的概述，请参阅：

* [有风险的登录恢复](flows.md#risky-sign-in-recovery)
* [有风险的登录已阻止](flows.md#risky-sign-in-blocked)  
* [Azure AD Identity Protection 中的登录体验](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>什么是用户风险策略？

Azure AD 会分析用户的每次登录。 分析的目的是检测伴随登录而来的可疑操作。 在 Azure AD 中，系统可以检测到的可疑操作也称为风险事件。 尽管某些风险事件可以实时检测到，但有些风险事件需要更长的时间才能检测到。 例如，若要检测异常位置的不可能行程，系统需要花费 14 天的初始学习期来学习用户的常规行为。 可通过多个选项解决检测到的风险事件。 例如，可以手动解决单个风险事件，也可以使用登录风险或用户风险条件访问策略来解决这些事件。

已检测到的但尚未解决的所有用户风险事件称为活动风险事件。 与用户关联的活动风险事件称为用户风险。 Azure AD 根据用户风险计算用户遭到入侵的可能性（低、中、高）。 这种可能性称为用户风险级别。

![用户风险](./media/howto-configure-risk-policies/11031.png)

用户风险策略是可为特定用户风险级别配置的自动响应。 使用用户风险策略可以阻止对资源的访问，或者要求更改密码，使用户帐户恢复安全状态。


## <a name="how-do-i-access-the-user-risk-policy"></a>如何访问用户风险策略？
   
用户风险策略位于 [Azure AD Identity Protection 页](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)上的“配置”部分。
   
![用户风险策略](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>用户风险策略设置

配置用户风险策略时，需要设置以下各项：

- 该策略应用到的用户和组：

    ![用户和组](./media/howto-configure-risk-policies/111.png)

- 触发该策略的登录风险级别：

    ![用户风险级别](./media/howto-configure-risk-policies/112.png)

- 当满足登录风险级别时要强制实施的访问类型：  

    ![访问](./media/howto-configure-risk-policies/113.png)

- 策略的状态：

    ![强制实施策略](./media/howto-configure-risk-policies/114.png)

策略配置对话框提供了一个选项用于评估配置的影响。

![估计影响](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>用户风险策略要点

可以设置用户风险安全策略，根据风险级别阻止用户登录。

![正在阻止](./media/howto-configure-risk-policies/116.png)


阻止登录：

* 避免针对受影响用户生成新的用户风险事件
* 允许管理员手动补救影响用户标识的风险事件，并将它还原到安全状态


























## <a name="best-practices"></a>最佳实践

选择“高”阈值可减少触发策略的次数，最大程度地降低对用户的影响。  

但是，它会从策略中排除标记为“低”和“中”风险的登录，而无法阻止攻击者利用遭到入侵的标识。

设置策略时：

- 排除没有/无法注册多重身份验证的用户

- 排除无法启用策略（例如无法访问技术支持）的区域中的用户

- 排除可能会生成大量误报的用户（开发人员、安全分析人员）

- 在首次实施策略期间，或者必须尽量减少向最终用户显示质询时，请使用“高”阈值。

- 如果组织需要更高的安全性，请使用“低”阈值。 选择“低”阈值会显示更多的用户登录质询，但可以提高安全性。

对于大多数组织而言，建议的默认值是设置“中”阈值的规则，以便在可用性与安全性之间取得均衡。





## <a name="next-steps"></a>后续步骤

 [第 9 频道：Azure AD 和标识展示：Identity Protection 预览版](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

