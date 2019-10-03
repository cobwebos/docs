---
title: 如何在 Azure Active Directory 标识保护中配置用户风险策略| Microsoft Docs
description: 了解如何配置“Azure AD 标识保护”用户风险策略。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92bfb921833d99a3538ffa8c4c5d16a9f0cd3acd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126283"
---
# <a name="how-to-configure-the-user-risk-policy"></a>如何：配置用户风险策略

Azure AD 使用用户风险检测用户帐户泄露的可能性。 管理员可将用户风险条件访问策略配置为自动对特定的用户风险级别做出响应。
 
本文提供配置用户风险策略所需的信息。

## <a name="what-is-a-user-risk-policy"></a>什么是用户风险策略？

Azure AD 会分析用户的每次登录。 分析的目的是检测伴随登录而来的可疑操作。 在 Azure AD 中, 系统可以检测到的可疑操作也称为风险检测。 尽管某些风险检测可以实时检测到, 但也存在需要更多时间的风险检测。 例如，若要检测异常位置的不可能行程，系统需要花费 14 天的初始学习期来学习用户的常规行为。 有几个选项可用于解决检测到的风险检测。 例如, 您可以手动解决各个风险检测, 也可以使用登录风险或用户风险条件性访问策略来解决它们。

为用户检测到并且未解决的所有风险检测称为 "活动风险检测"。 与用户关联的活动风险检测称为 "用户风险"。 Azure AD 根据用户风险计算用户遭到入侵的可能性（低、中、高）。 这种可能性称为用户风险级别。

![用户风险](./media/howto-user-risk-policy/1031.png)

用户风险策略是可为特定用户风险级别配置的自动响应。 使用用户风险策略可以阻止对资源的访问，或者要求更改密码，使用户帐户恢复安全状态。

## <a name="how-do-i-access-the-user-risk-policy"></a>如何访问用户风险策略？
   
登录风险策略位于[“Azure AD 标识保护”页](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)上的“配置”部分中。
   
![用户风险策略](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>策略设置

配置登录风险策略时，需要设置：

- 该策略应用到的用户和组：

    ![用户和组](./media/howto-user-risk-policy/11.png)

- 触发该策略的登录风险级别：

    ![用户风险级别](./media/howto-user-risk-policy/12.png)

- 当满足登录风险级别时要强制实施的访问类型：  

    ![访问权限](./media/howto-user-risk-policy/13.png)

- 策略的状态：

    ![强制实施策略](./media/howto-user-risk-policy/14.png)

策略配置对话框提供了一个选项用于评估配置的影响。

![估计影响](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>要点

可以设置用户风险安全策略，根据风险级别阻止用户登录。

![正在阻止](./media/howto-user-risk-policy/16.png)

阻止登录：

* 阻止为受影响的用户生成新的用户风险检测
* 使管理员能够手动修正影响用户标识的风险检测, 并将其还原到安全状态

## <a name="best-practices"></a>最佳实践

选择“高”阈值可减少触发策略的次数，最大程度地降低对用户的影响。
但是，这会从策略中排除标记为“低”和“中”风险的用户，因而无法保护以前疑似或已知遭到入侵的标识或设备。

设置策略时：

* 排除可能生成大量误报的用户（开发人员、安全分析人员）
* 排除无法启用策略（例如无法访问技术支持）的区域中的用户
* 在首次实施策略期间，或者必须尽量减少向最终用户显示质询时，请使用“高”阈值。
* 如果组织需要更高的安全性，请使用“低”阈值。 选择“低”阈值会显示更多的用户登录质询，但可以提高安全性。

对于大多数组织而言，建议的默认值是设置“中”阈值的规则，以便在可用性与安全性之间取得均衡。

如需相关用户体验的概述，请参阅：

* [遭到入侵的帐户恢复流程](flows.md#compromised-account-recovery)。  
* [遭到入侵的帐户阻止流程](flows.md#compromised-account-blocked)。  

**若要打开相关的配置对话框**：

- 在“Azure AD 标识保护”边栏选项卡上的“配置”部分中，单击“用户风险策略”。

    ![用户风险策略](./media/howto-user-risk-policy/1009.png "用户风险策略")

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview.md)。
