---
title: 解决 Azure AD Identity Protection 中的风险和解除阻止用户
description: 了解用于关闭活动风险检测的选项。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382102"
---
# <a name="remediate-risks-and-unblock-users"></a>修正风险和解除阻止用户

完成[调查](howto-identity-protection-investigate-risk.md)后，需要采取措施来纠正风险或取消阻止用户。 组织还可以选择使用其[风险策略](howto-identity-protection-configure-risk-policies.md)来启用自动修正。 组织应尝试在你的组织熟悉的一段时间内关闭所有风险检测。 Microsoft 建议尽快关闭事件，因为在处理风险时，时间很重要。

## <a name="remediation"></a>补救

计算称作“用户风险级别”的值时，会考虑到所有活动风险检测。 用户风险级别是反映帐户泄露可能性的一个指标（低、中、高）。 管理员希望能够关闭所有风险检测，以便受影响的用户不再面临风险。

某些风险检测可能会被标识保护标记为 "已关闭（系统）"，因为这些事件不再确定是危险的。

管理员可以使用以下选项进行修正：

- 带有风险策略的自我修正
- 手动重置密码
- 消除用户风险
- 手动关闭单个风险检测

### <a name="self-remediation-with-risk-policy"></a>带有风险策略的自我修正

如果你允许用户通过 Azure 多因素身份验证（MFA）和你的风险策略中的自助密码重置（SSPR）自行修正，则在检测到风险时，他们可以取消阻止自身。 然后将这些检测视为关闭。 用户必须先注册 Azure MFA 和 SSPR，才能在检测到风险时使用。

某些检测可能不会对用户需要进行自我修正的级别提出风险，但管理员仍应评估这些检测。 管理员可以确定需要执行其他措施，如[阻止来自位置的访问](../conditional-access/howto-conditional-access-policy-location.md)，或降低其策略中可接受的风险。

### <a name="manual-password-reset"></a>手动重置密码

如果需要使用用户风险策略来重置密码，则管理员可以关闭手动重置密码的用户的所有风险检测。

在为用户重置密码时，将为管理员提供两个选项：

- **生成临时密码** - 生成临时密码可以立即让标识恢复安全状态。 此方法要求联系受影响的用户，因为他们需要知道临时密码是什么。 由于密码是临时的，因此，在下一次登录期间，系统会提示用户将密码更改为新密码。

- **要求用户重置密码** - 要求用户重置密码可以实现自助恢复，而无需联系支持人员或管理员。 此方法仅适用于已注册 Azure MFA 和 SSPR 的用户。 对于尚未注册的用户，此选项不可用。

### <a name="dismiss-user-risk"></a>消除用户风险

如果密码重置不是你的选择，因为例如，用户已被删除，你可以选择取消用户风险检测。

单击 "**消除用户风险**" 时，所有事件都将关闭，受影响的用户不再有风险。 但是，此方法不会对现有密码产生影响，因为它不能将相关的标识恢复安全状态。 

### <a name="close-individual-risk-detections-manually"></a>手动关闭单个风险检测

可以手动关闭单个风险检测。 手动关闭风险检测可以降低用户风险级别。 通常，在响应相关调查时需手动关闭风险检测。 例如，在与用户沟通时发现不再需要某个活动风险检测。 
 
手动关闭风险检测时，可以选择执行以下任一操作来更改风险检测的状态：

- 确认用户已泄露
- 消除用户风险
- 确认登录安全
- 确认已泄露登录

## <a name="unblocking-users"></a>取消阻止用户

管理员可以选择基于其风险策略或调查阻止登录。 可能会出现基于登录或用户风险的块。

### <a name="unblocking-based-on-user-risk"></a>基于用户风险取消阻止

若要取消阻止帐户由于用户风险而被阻止，管理员可选择以下选项：

1. **重置密码** - 可以重置用户的密码。
1. **消除用户风险**-如果已达到配置的用户风险级别用于阻止访问，则用户风险策略会阻止用户。 可以通过消除用户风险或手动关闭已报告的风险检测来降低用户的风险级别。
1. **从策略中排除用户**-如果你认为登录策略的当前配置导致特定用户出现问题，你可以将用户排除在外。 有关详细信息，请参阅[如何：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md#exclusions)一文中的排除部分。
1. **禁用** - 如果认为策略配置导致所有用户出现问题，可禁用该策略。 有关详细信息，请参阅[如何：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)一文。

### <a name="unblocking-based-on-sign-in-risk"></a>基于登录风险取消阻止

若要基于登录风险取消阻止帐户，管理员可以使用以下选项：

1. 从熟悉的位置或设备登录 - 已阻止可疑登录的常见原因是尝试从不熟悉的位置或设备登录。 你的用户可以通过尝试从熟悉的位置或设备登录来快速确定此原因是否为阻止的原因。
1. **从策略中排除用户**-如果你认为登录策略的当前配置导致特定用户出现问题，你可以将用户排除在外。 有关详细信息，请参阅[如何：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md#exclusions)一文中的排除部分。
1. **禁用** - 如果认为策略配置导致所有用户出现问题，可禁用该策略。 有关详细信息，请参阅[如何：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)一文。

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview-identity-protection.md)。
