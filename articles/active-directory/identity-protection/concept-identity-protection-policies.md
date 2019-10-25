---
title: Azure AD Identity Protection 策略
description: 标识启用了 Identity Protection 的三个策略
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887411"
---
# <a name="identity-protection-policies"></a>Identity Protection 策略

Azure Active Directory Identity Protection 包括管理员可选择启用的三个默认策略。 这些策略包括有限的自定义项，但适用于大多数组织。 所有策略都允许排除用户（如[紧急访问权限或侵入权限管理员帐户](../users-groups-roles/directory-emergency-access.md)）。

![Identity Protection 策略](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 注册策略

Identity Protection 可帮助组织使用要求在登录时注册的条件性访问策略来推出 Azure 多重身份验证（MFA）。 启用此策略是确保组织中的新用户在第一天注册了 MFA 的好办法。 多重身份验证是在 Identity Protection 内实现风险事件的一种自我补救方法。 自修正允许用户自行采取措施来减少支持人员的呼叫量。

有关 Azure 多重身份验证的详细信息，请参阅[其工作原理： Azure 多重身份验证](../authentication/concept-mfa-howitworks.md)。

## <a name="sign-in-risk-policy"></a>登录风险策略

标识保护会分析每次登录时的信号，无论是实时的还是脱机的，并根据用户未执行登录的概率计算风险评分。 管理员可以根据此风险评分的决策作出决定，以强制实施组织要求。 管理员可以选择阻止访问、允许访问或允许访问，但需要多重身份验证。

如果检测到风险，用户可以执行多重身份验证以进行自我修正，并关闭有风险的登录事件，以防管理员不必要的干扰。

> [!NOTE] 
> 用户必须先注册 Azure 多重身份验证，然后才能触发登录风险策略。

### <a name="custom-conditional-access-policy"></a>自定义条件访问策略

管理员还可以选择创建自定义条件访问策略，包括作为分配条件的登录风险。 有关条件访问的详细信息，请参阅[什么是条件性访问？](../conditional-access/overview.md)

![自定义条件访问登录风险策略](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>用户风险策略

标识保护可以计算用户行为的正常情况，并使用它来确定其风险决策。 用户风险是指标识已泄露的概率的计算。 管理员可以根据此风险评分的决策作出决定，以强制实施组织要求。 管理员可以选择阻止访问、允许访问或允许访问，但需要更改密码才能使用[Azure AD 自助密码重置](../authentication/howto-sspr-deployment.md)。

如果检测到风险，用户可以执行自助服务密码重置以自行修正并关闭用户风险事件，以防管理员不必要的干扰。

> [!NOTE] 
> 用户必须先注册了自助服务密码重置，然后才能触发用户风险策略。

## <a name="next-steps"></a>后续步骤

- [启用 Azure AD 自助服务密码重置](../authentication/howto-sspr-deployment.md)

- [启用 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)

- [启用 Azure 多重身份验证注册策略](howto-identity-protection-configure-mfa-policy.md)

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)
