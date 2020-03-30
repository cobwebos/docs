---
title: Azure AD 标识保护策略
description: 标识使用身份保护启用的三个策略
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887411"
---
# <a name="identity-protection-policies"></a>标识保护策略

Azure 活动目录标识保护包括管理员可以选择启用的三个默认策略。 这些策略包括有限的自定义，但适用于大多数组织。 所有策略都允许排除用户，如[紧急访问或碎玻璃管理员帐户](../users-groups-roles/directory-emergency-access.md)。

![标识保护策略](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 注册策略

身份保护可帮助组织使用需要在登录时进行注册的条件访问策略来推出 Azure 多重身份验证 （MFA）。 启用此策略是确保组织中的新用户在第一天注册 MFA 的好方法。 多重身份验证是身份保护中风险事件的自修复方法之一。 自我修复允许用户自行采取行动，以减少帮助台呼叫量。

有关 Azure 多重身份验证的详细信息，请参阅文章"[如何工作：Azure 多重身份验证](../authentication/concept-mfa-howitworks.md)"。

## <a name="sign-in-risk-policy"></a>登录风险策略

身份保护分析来自每个登录（实时和脱机）的信号，并根据用户未执行登录的概率计算风险评分。 管理员可以基于此风险评分信号做出决策，以强制实施组织要求。 管理员可以选择阻止访问、允许访问或允许访问，但需要多重身份验证。

如果检测到风险，用户可以执行多重身份验证以自我修复和关闭危险的登录事件，以防止管理员出现不必要的噪音。

> [!NOTE] 
> 在触发登录风险策略之前，用户必须以前已注册 Azure 多重身份验证。

### <a name="custom-conditional-access-policy"></a>自定义条件访问策略

管理员还可以选择创建自定义条件访问策略，包括登录风险作为分配条件。 有关条件访问的详细信息，请参阅文章"[什么是条件访问？](../conditional-access/overview.md)

![自定义条件访问登录风险策略](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>用户风险策略

身份保护可以计算它认为用户行为的正常内容，并用它来根据用户的风险做出决策。 用户风险是计算标识被泄露的可能性。 管理员可以基于此风险评分信号做出决策，以强制实施组织要求。 管理员可以选择阻止访问、允许访问或允许访问，但需要使用[Azure AD 自助服务密码重置更改密码](../authentication/howto-sspr-deployment.md)。

如果检测到风险，用户可以执行自助服务密码重置以自我修复并关闭用户风险事件，以防止管理员出现不必要的噪音。

> [!NOTE] 
> 在触发用户风险策略之前，用户必须以前已注册进行自助服务密码重置。

## <a name="next-steps"></a>后续步骤

- [启用 Azure AD 自助服务密码重置](../authentication/howto-sspr-deployment.md)

- [启用 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)

- [启用 Azure 多重身份验证注册策略](howto-identity-protection-configure-mfa-policy.md)

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)
