---
title: Azure AD 标识保护策略
description: 识别已启用了标识保护的三个策略
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7587ed6d414a69cff67aca9446aebf6260c99fcd
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736494"
---
# <a name="identity-protection-policies"></a>标识保护策略

Azure Active Directory 标识保护包含三个管理员可选择启用的默认策略。 这些策略包括有限的自定义项，但适用于大多数组织。 所有策略都允许排除部分用户（如[应急访问或紧急情况管理员帐户](../users-groups-roles/directory-emergency-access.md)）。

![标识保护策略](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 注册策略

标识保护可帮助组织推出使用条件访问策略的 Azure 多重身份验证 (MFA)，该策略要求在登录时进行注册。 启用此策略可更好地确保组织中的新用户在第一天就已注册 MFA。 在标识保护中，多重身份验证是针对风险事件的一种自动修正方法。 用户可通过自动修正自行采取措施来减少支持人员呼叫量。

有关 Azure 多重身份验证的更多信息，请参阅文章[工作原理：Azure 多重身份验证](../authentication/concept-mfa-howitworks.md)。

## <a name="sign-in-risk-policy"></a>登录风险策略

标识保护会分析每次登录时的信号，无论是实时还是脱机，并根据登录并非是用户操作的概率计算风险评分。 管理员可以根据此风险评分信号做出决策，以强制实施组织要求。 管理员可以选择阻止访问、允许访问或允许访问但需要多重身份验证。

如果检测到风险，用户可以执行多重身份验证以进行自我修正，并关闭有风险的登录事件，以避免为管理员带来不必要的干扰。

> [!NOTE] 
> 用户必须预先注册了 Azure 多重身份验证，然后才能触发登录风险策略。

### <a name="custom-conditional-access-policy"></a>自定义条件访问策略

管理员还可以选择创建自定义条件访问策略，包括将登录风险作为分配条件。 有关条件访问策略中将风险作为条件的详细信息，请参阅文章[条件访问：条件](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![自定义条件访问登录风险策略](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>用户风险策略

标识保护可评估用户的行为是否正常，并使用它来作为判断用户风险的基础。 用户风险是指计算身份已泄露的概率。 管理员可以根据此风险评分信号做出决策，以强制实施组织要求。 管理员可以选择阻止访问、允许访问或允许访问但需要使用 [Azure AD 自助式密码重置](../authentication/howto-sspr-deployment.md)进行密码更改。

如果检测到风险，用户可以执行自助式密码重置来进行自我修正，并关闭用户风险事件，以避免为管理员带来不必要的干扰。

> [!NOTE] 
> 用户必须预先注册了自助式密码重置，然后才能触发用户风险策略。

## <a name="next-steps"></a>后续步骤

- [启用 Azure AD 自助式密码重置](../authentication/howto-sspr-deployment.md)

- [启用 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)

- [启用 Azure 多重身份验证注册策略](howto-identity-protection-configure-mfa-policy.md)

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)
