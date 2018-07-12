---
title: 如何配置安全警报 | Microsoft 文档
description: 了解如何为 Azure Privileged Identity Management 扩展配置安全警报。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fee4344be2875140a2e9c35bd83eb668bd1efa3f
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952450"
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中配置安全警报
## <a name="security-alerts"></a>安全警报
如果环境中有可疑活动或不安全活动，Azure Privileged Identity Management (PIM) 将生成警报。 触发警报时，它显示在 PIM 仪表板上。 选择警报以查看列出了已触发该警报的用户或角色的报告。

![PIM 仪表板安全警报 - 屏幕截图](./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png)

| 警报 | Severity | 触发器 | 建议 |
| --- | --- | --- | --- |
| **在 PIM 之外分配的角色** |高 |用户已在 PIM 接口之外永久分配特权角色。 |检查列表中的用户，并取消他们从 PIM 以外分配到的特权角色。 |
| **角色激活过于频繁** |中型 |在设置中允许的时间内，重新激活同一角色的次数过多。 |请与用户联系，了解他们为何激活该角色这么多次。 可能是时间限制太短，他们无法完成任务，也可能是他们使用脚本自动激活角色。 请确保用户角色的激活持续时间设置得足够长，以便他们能够执行任务。 |
| **角色无需多重身份验证进行激活** |中型 |存在未在设置中启用 MFA 的角色。 |对于大多数高特权角色，我们需要 MFA，但强烈建议为激活所有角色启用 MFA。 |
| **用户不使用其特权角色** |低 |存在最近未激活其角色的符合条件的管理员。 |开始访问审阅，确定不再需要访问的用户。 |
| **全局管理员过多** |低 |存在的全局管理员数量多于建议数量。 |如果有大量全局管理员，用户获取的权限可能超出需求。 将用户移到较少特权的角色，或使某些用户符合角色条件，而非永久分配。 |

### <a name="severity"></a>Severity
* **高**：因策略冲突需要立即采取措施。 
* **中**：不需要立即采取措施但有潜在的策略冲突。
* **低**：不需要立即采取措施，但建议考虑可取的策略更改。

## <a name="configure-security-alert-settings"></a>配置安全警报设置
可以在 PIM 中自定义某些安全警报，以处理环境和安全目标。 按照以下步骤访问设置边栏选项卡：

1. 登录 [Azure 门户](https://portal.azure.com/)，并从仪表板中选择“Azure AD Privileged Identity Management”磁贴。
2. 依次选择“托管的特权角色” > “设置” > “警报设置”。
   
    ![导航到安全警报设置](./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png)

### <a name="roles-are-being-activated-too-frequently-alert"></a>“角色激活过于频繁”警报
如果用户在指定期限内多次激活同一特权角色，将触发此警报。 可以同时配置时间段和激活次数。

* **激活续订时间范围**：以天、小时、分钟和秒为单位指定要用于跟踪可疑续订的时间段。
* **激活续订次数**：在所选时间范围内指定你认为值得发送警报的激活次数（范围从 2 到 100）。 可通过移动滑块或在文本框中键入数字更改此设置。

### <a name="there-are-too-many-global-administrators-alert"></a>“全局管理员过多”警报
如果满足两个不同的条件并且可以同时配置这两个条件，PIM 将触发此警报。 首先，需要达到全局管理员的某个特定阈值。 其次，总角色分配的特定百分比必须是全局管理员。 如果只满足其中一个度量，不会显示该警报。  

* **最少全局管理员数**：指定你认为不安全的全局管理员数（范围从 2 到 100）。
* **全局管理员百分比**：指定在环境中不安全的管理员（属于全局管理员）百分比（范围介于 0% 到 100%）。

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>“管理员不使用其特权角色”警报
如果用户在一定时间过后没有激活角色，将触发此警报。

* **天数**：指定用户不激活角色的天数（范围介于 0 到 100）.

## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
