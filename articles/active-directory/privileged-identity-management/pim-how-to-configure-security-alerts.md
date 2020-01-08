---
title: PIM 中 Azure AD 角色的安全警报-Azure AD |Microsoft Docs
description: 配置 Azure Active Directory 中 Azure AD 角色 Privileged Identity Management 的安全警报。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32fc0f473ccd8a094f2b45f383ffdb4f6b4ac121
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430160"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>为 Privileged Identity Management 中 Azure AD 角色配置安全警报

当 Azure Active Directory （Azure AD）组织中存在可疑或不安全的活动时，Privileged Identity Management （PIM）会生成警报。 触发警报时，警报将显示在 Privileged Identity Management 仪表板上。 选择警报以查看列出了已触发该警报的用户或角色的报告。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从2019年11月开始，Privileged Identity Management 的 Azure AD 角色部分更新为与 Azure 资源角色体验相匹配的新版本。 这将创建其他功能以及对[现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中所遵循的过程取决于当前所用 Privileged Identity Management 的版本。 按照本部分中的步骤确定 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本后，可以在本文中选择与该版本匹配的过程。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 角色的用户登录到[Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 如果在 "概述" 页顶部有一个横幅，请按照本文的 "**新版本**" 选项卡中的说明进行操作。 否则，请按照 "以前的**版本**" 选项卡中的说明进行操作。

    ![Azure AD 角色新版本](./media/pim-how-to-add-role-to-user/pim-new-version.png)

按照本文中的步骤来调查 Azure AD 角色的安全警报。

# <a name="previous-versiontabprevious"></a>[先前版本](#tab/previous)

![Azure AD 角色-警报窗格列出警报和严重性](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>安全警报

本部分列出了 Azure AD 角色的所有安全警报，以及如何修复和阻止。 严重性的含义如下：

- **高**：因策略冲突需要立即采取措施。
- **中**：不需要立即采取措施但有潜在的策略冲突。
- **低**：不需要立即采取措施，但建议考虑可取的策略更改。

### <a name="administrators-arent-using-their-privileged-roles"></a>管理员不使用其特权角色

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 为用户分配他们不需要的特权角色会增大受攻击的可能性。 攻击者更容易忽略不经常使用的帐户。 |
| **如何修复？** | 查看列表中的用户并将其从不需要的特权角色中删除。 |
| **预防** | 仅向具有业务理由的用户分配特权角色。 </br>安排定期的[访问评审](pim-how-to-start-security-review.md)，以确认用户是否仍需要访问权限。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **触发器** | 如果用户在未激活角色的情况下经历了指定的天数，则触发。 |
| **天数** | 此设置指定用户无需激活角色即可执行的最大天数（0到100）。|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>角色无需多重身份验证进行激活

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 如果没有多重身份验证，攻击方用户可以激活特权角色。 |
| **如何修复？** | 查看角色列表，并要求对每个角色进行[多重身份验证](pim-how-to-change-default-settings.md)。 |
| **预防** | 针对每个角色[要求执行 MFA](pim-how-to-change-default-settings.md)。  |
| **门户中的缓解措施** | 使得激活特权角色需要多重身份验证。 |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>组织没有 Azure AD Premium P2

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 当前 Azure AD 组织没有 Azure AD Premium P2。 |
| **如何修复？** | 查看有关 [Azure AD 版本](../fundamentals/active-directory-whatis.md)的信息。 升级到 Azure AD Premium P2。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>可能有过时的帐户充当特权角色

| | |
| --- | --- |
| **严重性** | 中型 |
| **为何收到此警报？** | 特权角色中的帐户在过去的90天内未更改其密码。 这些帐户可能是未维护且易受攻击者攻击的服务帐户或共享帐户。 |
| **如何修复？** | 请检查列表中的帐户。 如果它们不再需要访问权限，请将其从特权角色中删除。 |
| **预防** | 确保当知道密码的用户有变化时，共享的帐户会轮换使用强密码。 </br>使用[访问评审](pim-how-to-start-security-review.md)定期审查具有特权角色的帐户，并删除不再需要的角色分配。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **最佳做法** | 使用密码进行身份验证并分配给高度特权的管理角色（如全局管理员或安全管理员）的共享、服务和紧急访问帐户应在以下情况下旋转其密码：<ul><li>发生涉及误用或泄露管理访问权限的安全事件后</li><li>任何用户的权限被更改而导致他们不再是管理员之后（例如，一名曾是管理员的员工离开了 IT 或组织）</li><li>固定时间间隔（例如，每季度或每年），即使没有任何已知的安全漏洞或 IT 人员变动</li></ul>由于多个用户有权限访问这些帐户的凭据，因此应轮换这些凭据以确保已失去其角色的人员无法再访问帐户。 [了解详细信息](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>在 Privileged Identity Management 之外分配角色

| | |
| --- | --- |
| **严重性** | 高 |
| **为何收到此警报？** | 在 Privileged Identity Management 之外进行的特权角色分配未得到正确监视，并且可能表示存在活动的攻击。 |
| **如何修复？** | 查看列表中的用户，并从 Privileged Identity Management 之外分配的特权角色中删除这些用户。 |
| **预防** | 调查向用户分配 Privileged Identity Management 之外的特权角色的位置，并禁止将来分配。 |
| **门户中的缓解措施** | 从特权角色中删除用户。 |

### <a name="there-are-too-many-global-administrators"></a>全局管理员过多

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 全局管理员是特权最高的角色。 如果全局管理员受到侵害，攻击者将获得对其所有权限的访问权限，从而使整个系统面临风险。 |
| **如何修复？** | 查看列表中的用户，并删除不完全需要全局管理员角色的任何用户。 </br>改为向这些用户分配低特权角色。 |
| **预防** | 为用户分配他们所需的最低特权角色。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **触发器** | 如果满足两个不同的条件并且可以同时配置这两个条件，将触发此警报。 首先，需要达到全局管理员的某个阈值。 其次，您的总角色分配百分比必须是全局管理员。 如果只满足其中一个度量，不会显示该警报。 |
| **全局管理员的最少数目** | 此设置指定从2到100的全局管理员的数量，你认为这对于你的 Azure AD 组织而言太少。 |
| **全局管理员百分比** | 此设置指定作为全局管理员的管理员的最小百分比，从0% 到100%，低于此值后，你不希望 Azure AD 组织达到 dip。 |

### <a name="roles-are-being-activated-too-frequently"></a>角色激活过于频繁

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 同一用户多次激活同一特权角色是受到攻击的迹象。 |
| **如何修复？** | 检查列表中的用户，并确保用户特权角色的[激活持续时间](pim-how-to-change-default-settings.md)设置得足够长，使他们能够执行任务。 |
| **预防** | 确保特权角色的[激活持续时间](pim-how-to-change-default-settings.md)设置得足够长，使用户能够执行其任务。</br>对于具有多个管理员共享帐户的特权角色，[需要多重身份验证](pim-how-to-change-default-settings.md)。 |
| **门户中的缓解措施** | N/A |
| **触发器** | 如果用户在指定期限内多次激活同一特权角色，将触发此警报。 可以同时配置时间段和激活次数。 |
| **激活续订时间范围** | 此设置以天、小时、分钟和秒为单位指定要用于跟踪可疑续订的时间段。 |
| **激活续订次数** | 此设置指定从2到100的激活次数，你希望在选择的时间范围内收到通知。 可通过移动滑块或在文本框中键入数字更改此设置。 |

## <a name="configure-security-alert-settings"></a>配置安全警报设置

你可以在 Privileged Identity Management 中自定义一些安全警报，以满足你组织的需求和安全目标。 执行以下步骤打开安全警报设置：

1. 在 Azure AD 中打开**Privileged Identity Management** 。

1. 选择**Azure AD 角色**。

1. 选择 "**设置**"，然后选择 "**警报**"。

    ![Azure AD 角色-所选警报的设置](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. 选择警报名称以配置该警报的设置。

    ![对于选定的警报，安全警报设置窗格](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

# <a name="new-versiontabnew"></a>[新版本](#tab/new)

![Azure AD 角色-警报窗格列出警报和严重性](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>安全警报

本部分列出了 Azure AD 角色的所有安全警报，以及如何修复和阻止。 严重性的含义如下：

- **高**：因策略冲突需要立即采取措施。
- **中**：不需要立即采取措施但有潜在的策略冲突。
- **低**：不需要立即采取措施，但建议考虑可取的策略更改。

### <a name="administrators-arent-using-their-privileged-roles"></a>管理员不使用其特权角色

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 为用户分配他们不需要的特权角色会增大受攻击的可能性。 攻击者更容易忽略不经常使用的帐户。 |
| **如何修复？** | 查看列表中的用户并将其从不需要的特权角色中删除。 |
| **预防** | 仅向具有业务理由的用户分配特权角色。 </br>安排定期的[访问评审](pim-how-to-start-security-review.md)，以确认用户是否仍需要访问权限。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **触发器** | 如果用户在未激活角色的情况下经历了指定的天数，则触发。 |
| **天数** | 此设置指定用户无需激活角色即可执行的最大天数（0到100）。|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>角色无需多重身份验证进行激活

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 如果没有多重身份验证，攻击方用户可以激活特权角色。 |
| **如何修复？** | 查看角色列表，并要求对每个角色进行[多重身份验证](pim-how-to-change-default-settings.md)。 |
| **预防** | 针对每个角色[要求执行 MFA](pim-how-to-change-default-settings.md)。  |
| **门户中的缓解措施** | 使得激活特权角色需要多重身份验证。 |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>组织没有 Azure AD Premium P2

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 当前 Azure AD 组织没有 Azure AD Premium P2。 |
| **如何修复？** | 查看有关 [Azure AD 版本](../fundamentals/active-directory-whatis.md)的信息。 升级到 Azure AD Premium P2。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>可能有过时的帐户充当特权角色

| | |
| --- | --- |
| **严重性** | 中型 |
| **为何收到此警报？** | 特权角色中的帐户在过去的90天内未更改其密码。 这些帐户可能是未维护且易受攻击者攻击的服务帐户或共享帐户。 |
| **如何修复？** | 请检查列表中的帐户。 如果它们不再需要访问权限，请将其从特权角色中删除。 |
| **预防** | 确保当知道密码的用户有变化时，共享的帐户会轮换使用强密码。 </br>使用[访问评审](pim-how-to-start-security-review.md)定期审查具有特权角色的帐户，并删除不再需要的角色分配。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **最佳做法** | 使用密码进行身份验证并分配给高度特权的管理角色（如全局管理员或安全管理员）的共享、服务和紧急访问帐户应在以下情况下旋转其密码：<ul><li>发生涉及误用或泄露管理访问权限的安全事件后</li><li>任何用户的权限被更改而导致他们不再是管理员之后（例如，一名曾是管理员的员工离开了 IT 或组织）</li><li>固定时间间隔（例如，每季度或每年），即使没有任何已知的安全漏洞或 IT 人员变动</li></ul>由于多个用户有权限访问这些帐户的凭据，因此应轮换这些凭据以确保已失去其角色的人员无法再访问帐户。 [了解详细信息](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>在 Privileged Identity Management 之外分配角色

| | |
| --- | --- |
| **严重性** | 高 |
| **为何收到此警报？** | 在 Privileged Identity Management 之外进行的特权角色分配未得到正确监视，并且可能表示存在活动的攻击。 |
| **如何修复？** | 查看列表中的用户，并从 Privileged Identity Management 之外分配的特权角色中删除这些用户。 |
| **预防** | 调查向用户分配 Privileged Identity Management 之外的特权角色的位置，并禁止将来分配。 |
| **门户中的缓解措施** | 从特权角色中删除用户。 |

### <a name="there-are-too-many-global-administrators"></a>全局管理员过多

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 全局管理员是特权最高的角色。 如果全局管理员受到侵害，攻击者将获得对其所有权限的访问权限，从而使整个系统面临风险。 |
| **如何修复？** | 查看列表中的用户，并删除不完全需要全局管理员角色的任何用户。 </br>改为向这些用户分配低特权角色。 |
| **预防** | 为用户分配他们所需的最低特权角色。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **触发器** | 如果满足两个不同的条件并且可以同时配置这两个条件，将触发此警报。 首先，需要达到全局管理员的某个阈值。 其次，您的总角色分配百分比必须是全局管理员。 如果只满足其中一个度量，不会显示该警报。 |
| **全局管理员的最少数目** | 此设置指定从2到100的全局管理员的数量，你认为这对于你的 Azure AD 组织而言太少。 |
| **全局管理员百分比** | 此设置指定作为全局管理员的管理员的最小百分比，从0% 到100%，低于此值后，你不希望 Azure AD 组织达到 dip。 |

### <a name="roles-are-being-activated-too-frequently"></a>角色激活过于频繁

| | |
| --- | --- |
| **严重性** | 低 |
| **为何收到此警报？** | 同一用户多次激活同一特权角色是受到攻击的迹象。 |
| **如何修复？** | 检查列表中的用户，并确保用户特权角色的[激活持续时间](pim-how-to-change-default-settings.md)设置得足够长，使他们能够执行任务。 |
| **预防** | 确保特权角色的[激活持续时间](pim-how-to-change-default-settings.md)设置得足够长，使用户能够执行其任务。</br>对于具有多个管理员共享帐户的特权角色，[需要多重身份验证](pim-how-to-change-default-settings.md)。 |
| **门户中的缓解措施** | N/A |
| **触发器** | 如果用户在指定期限内多次激活同一特权角色，将触发此警报。 可以同时配置时间段和激活次数。 |
| **激活续订时间范围** | 此设置以天、小时、分钟和秒为单位指定要用于跟踪可疑续订的时间段。 |
| **激活续订次数** | 此设置指定从2到100的激活次数，你希望在选择的时间范围内收到通知。 可通过移动滑块或在文本框中键入数字更改此设置。 |

## <a name="configure-security-alert-settings"></a>配置安全警报设置

从“警报”页转到“设置”。

![突出显示了设置的 "警报" 页](media/pim-how-to-configure-security-alerts/alert-settings.png)

在各个警报上自定义设置以适应你的环境和安全目标。

![用于启用和配置设置的警报的 "设置" 页](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

---

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)
