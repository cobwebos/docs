---
title: 在 PIM 中为 Azure AD 目录角色配置安全警报 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中为 Azure AD 目录角色配置安全警报。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 11/01/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e7204c223681b9a33c439b0d9fc653167422384a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011691"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>在 PIM 中为 Azure AD 目录角色配置安全警报

如果环境中有可疑活动或不安全的活动，Azure AD Privileged Identity Management (PIM) 将生成警报。 触发警报时，它显示在 PIM 仪表板上。 选择警报以查看列出了已触发该警报的用户或角色的报告。

![PIM 安全警报 - 屏幕截图](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>安全警报

本部分列出目录角色的所有安全警报，以及如何修复和防止这些警报。 严重性的含义如下：

* **高**：因策略冲突需要立即采取措施。
* **中**：不需要立即采取措施但有潜在的策略冲突。
* **低**：不需要立即采取措施，但建议考虑可取的策略更改。

### <a name="roles-are-being-assigned-outside-of-pim"></a>在 PIM 之外分配的角色

| | |
| --- | --- |
| **严重性** | 高 |
| **为何收到此警报？** | 在 PIM 外部进行的特权角色分配未受到正确的监视，可能表示正遭到攻击。 |
| **如何修复？** | 检查列表中的用户，并将其从 PIM 外部分配的特权角色中删除。 |
| **预防** | 调查在 PIM 外部的哪个位置为用户分配了特权角色，并禁止将来在该位置分配角色。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>可能有过时的帐户充当特权角色

| | |
| --- | --- |
| **严重性** | 中型 |
| **为何收到此警报？** | 最近未更改其密码的帐户可能是不受维护的服务或共享帐户。 这些充当特权角色的帐户容易受到攻击。 |
| **如何修复？** | 请检查列表中的帐户。 如果它们不再需要访问权限，请将其从特权角色中删除。 |
| **预防** | 确保当知道密码的用户有变化时，共享的帐户会轮换使用强密码。 </br>使用访问评审定期审查具有特权角色的帐户，并删除不再需要的角色分配。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |

### <a name="users-arent-using-their-privileged-roles"></a>用户不使用其特权角色

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 为用户分配他们不需要的特权角色会增大受攻击的可能性。 攻击者更容易忽略不经常使用的帐户。 |
| **如何修复？** | 检查列表中的用户，并将其从不需要的特权角色中删除。 |
| **预防** | 仅将特权角色分配给有业务需要的用户。 </br>安排定期的访问评审，以确认用户是否仍需要访问权限。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **触发器** | 如果用户在一定时间后未激活角色，将触发此警报。 |
| **天数** | 此设置指定用户可以不激活角色的天数（0 到 100）。|

### <a name="there-are-too-many-global-administrators"></a>全局管理员过多

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 全局管理员是特权最高的角色。 如果全局管理员遭到入侵，则攻击者可以获取其所有访问权限，使整个系统面临风险。 |
| **如何修复？** | 检查列表中的用户，并删除不是绝对需要全局管理员角色的所有用户。 </br>为这些用户分配特权更低的角色。 |
| **预防** | 为用户分配他们所需的最低特权角色。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **触发器** | 如果满足两个不同的条件并且可以同时配置这两个条件，将触发此警报。 首先，需要达到全局管理员的某个特定阈值。 其次，总角色分配的特定百分比必须是全局管理员。 如果只满足其中一个度量，不会显示该警报。 |
| **全局管理员的最少数目** | 此设置指定你认为不安全的全局管理员数量（2 到 100）。 |
| **全局管理员百分比** | 此设置指定环境中不安全的全局管理员最小百分比（0% 到 100%）。 |

### <a name="roles-are-being-activated-too-frequently"></a>角色激活过于频繁

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 同一用户多次激活同一特权角色是受到攻击的迹象。 |
| **如何修复？** | 检查列表中的用户，并确保用户特权角色的[激活持续时间](pim-how-to-change-default-settings.md)设置得足够长，使他们能够执行任务。 |
| **预防** | 确保特权角色的[激活持续时间](pim-how-to-change-default-settings.md)设置得足够长，使用户能够执行其任务。</br>针对其帐户由多个管理员共享的特权角色[要求执行 MFA](pim-how-to-change-default-settings.md)。 |
| **门户中的缓解措施** | 不适用 |
| **触发器** | 如果用户在指定期限内多次激活同一特权角色，将触发此警报。 可以同时配置时间段和激活次数。 |
| **激活续订时间范围** | 此设置以天、小时、分钟和秒为单位指定要用于跟踪可疑续订的时间段。 |
| **激活续订次数** | 此设置在所选时间范围内指定你认为值得发送警报的激活次数（2 到 100）。 可通过移动滑块或在文本框中键入数字更改此设置。 |

### <a name="roles-dont-require-mfa-for-activation"></a>角色不需要 MFA 即可激活

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 如果不执行 MFA，则遭到入侵的用户可以激活特权角色。 |
| **如何修复？** | 检查角色列表，并针对每个角色[要求执行 MFA](pim-how-to-change-default-settings.md)。 |
| **预防** | 针对每个角色[要求执行 MFA](pim-how-to-change-default-settings.md)。  |
| **门户中的缓解措施** | 要求在激活特权角色时执行 MFA。 |

## <a name="configure-security-alert-settings"></a>配置安全警报设置

可以在 PIM 中自定义某些安全警报，以处理环境和安全目标。 执行以下步骤打开安全警报设置：

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 角色”。

1. 依次单击“设置”、“警报”。

    ![导航到安全警报设置](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. 单击警报名称以配置该警报的设置。

    ![安全警报设置](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中配置 Azure AD 目录角色设置](pim-how-to-change-default-settings.md)
