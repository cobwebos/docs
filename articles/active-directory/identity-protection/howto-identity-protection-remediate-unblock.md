---
title: Remediate risks and unblock users in Azure AD Identity Protection
description: Learn about the options you have close active risk detections.
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
# <a name="remediate-risks-and-unblock-users"></a>Remediate risks and unblock users

After completing your [investigation](howto-identity-protection-investigate-risk.md), you will want to take action to remediate the risk or unblock users. Organizations also have the option to enable automated remediation using their [risk policies](howto-identity-protection-configure-risk-policies.md). Organizations should try to close all risk detections that they are presented with in a time period your organization is comfortable with. Microsoft recommends closing events as soon as possible because time matters when working with risk.

## <a name="remediation"></a>补救

All active risk detections contribute to the calculation of a value called user risk level. 用户风险级别是反映帐户泄露可能性的一个指标（低、中、高）。 As an administrator, you want to get all risk detections closed, so that the affected users are no longer at risk.

Some risks detections may be marked by Identity Protection as "Closed (system)" because the events were no longer determined to be risky.

Administrators have the following options to remediate:

- Self-remediation with risk policy
- 手动重置密码
- 消除用户风险
- Close individual risk detections manually

### <a name="self-remediation-with-risk-policy"></a>Self-remediation with risk policy

If you allow users to self-remediate, with Azure Multi-Factor Authentication (MFA) and self-service password reset (SSPR) in your risk policies, they can unblock themselves when risk is detected. These detections are then considered closed. Users must have previously registered for Azure MFA and SSPR in order to use when risk is detected.

Some detections may not raise risk to the level where a user self-remediation would be required but administrators should still evaluate these detections. Administrators may determine that additional measures are necessary like [blocking access from locations](../conditional-access/howto-conditional-access-policy-location.md) or lowering the acceptable risk in their policies.

### <a name="manual-password-reset"></a>手动重置密码

If requiring a password reset using a user risk policy is not an option, administrators can close all risk detections for a user with a manual password reset.

Administrators are given two options when resetting a password for their users:

- **生成临时密码** - 生成临时密码可以立即让标识恢复安全状态。 This method requires contacting the affected users because they need to know what the temporary password is. Because the password is temporary, the user is prompted to change the password to something new during the next sign-in.

- **要求用户重置密码** - 要求用户重置密码可以实现自助恢复，而无需联系支持人员或管理员。 This method only applies to users that are registered for Azure MFA and SSPR. For users that have not been registered, this option isn't available.

### <a name="dismiss-user-risk"></a>消除用户风险

If a password reset is not an option for you, because for example the user has been deleted, you can choose to dismiss user risk detections.

When you click **Dismiss user risk**, all events are closed and the affected user is no longer at risk. 但是，此方法不会对现有密码产生影响，因为它不能将相关的标识恢复安全状态。 

### <a name="close-individual-risk-detections-manually"></a>Close individual risk detections manually

You can close individual risk detections manually. By closing risk detections manually, you can lower the user risk level. Typically, risk detections are closed manually in response to a related investigation. For example, when talking to a user reveals that an active risk detection is not required anymore. 
 
When closing risk detections manually, you can choose to take any of the following actions to change the status of a risk detection:

- Confirm user compromised
- 消除用户风险
- Confirm sign-in safe
- Confirm sign-in compromised

## <a name="unblocking-users"></a>Unblocking users

An administrator may choose to block a sign-in based on their risk policy or investigations. A block may occur based on either sign-in or user risk.

### <a name="unblocking-based-on-user-risk"></a>Unblocking based on user risk

To unblock an account blocked due to user risk, administrators have the following options:

1. **重置密码** - 可以重置用户的密码。
1. **Dismiss user risk** - The user risk policy blocks a user if the configured user risk level for blocking access has been reached. You can reduce a user's risk level by dismissing user risk or manually closing reported risk detections.
1. **Exclude the user from policy** - If you think that the current configuration of your sign-in policy is causing issues for specific users, you can exclude the users from it. For more information, see the section Exclusions in the article [How To: Configure and enable risk policies](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **禁用** - 如果认为策略配置导致所有用户出现问题，可禁用该策略。 For more information, see the article [How To: Configure and enable risk policies](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Unblocking based on sign-in risk

To unblock an account based on sign-in risk, administrators have the following options:

1. 从熟悉的位置或设备登录 - 已阻止可疑登录的常见原因是尝试从不熟悉的位置或设备登录。 Your users can quickly determine whether this reason is the blocking reason by trying to sign-in from a familiar location or device.
1. **Exclude the user from policy** - If you think that the current configuration of your sign-in policy is causing issues for specific users, you can exclude the users from it. For more information, see the section Exclusions in the article [How To: Configure and enable risk policies](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **禁用** - 如果认为策略配置导致所有用户出现问题，可禁用该策略。 For more information, see the article [How To: Configure and enable risk policies](howto-identity-protection-configure-risk-policies.md).

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview-identity-protection.md)。
