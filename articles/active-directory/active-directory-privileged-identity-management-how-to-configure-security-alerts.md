---
title: "如何配置安全警报 | Microsoft 文档"
description: "了解如何为 Azure Privileged Identity Management 扩展配置安全警报。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4e0c911a-36c6-42a0-8f79-a01c03d2d04f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: dd8987a05c71643b49e0ac9e3c8ae43d47743225


---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中配置安全警报
## <a name="security-alerts"></a>安全警报
如果你的环境中有可疑活动或不安全活动，Azure Privileged Identity Management (PIM) 将生成警报。 触发警报时，它显示在 PIM 仪表板上。 选择警报以查看列出了已触发该警报的用户或角色的报告。

![PIM 仪表板安全警报 - 屏幕截图][1]

| 警报 | 触发器 | 建议 |
| --- | --- | --- |
| **在 PIM 之外分配的角色** |管理员已在 PIM 接口之外永久分配给角色。 |审阅新角色分配 由于其他服务仅可以分配永久管理员，如有必要，将其更改为符合条件的分配。 |
| **角色激活过于频繁** |在设置中允许的时间内，重新激活同一角色的次数过多。 |请与用户联系，了解他们为何激活该角色这么多次。 可能时间限制太短，他们无法完成任务，也可能他们使用脚本自动激活角色。 |
| **角色无需多重身份验证进行激活** |存在未在设置中启用 MFA 的角色。 |对于大多数高特权角色，我们需要 MFA，但强烈建议你为激活所有角色启用 MFA。 |
| **管理员不使用其特权角色** |存在最近未激活其角色的符合条件的管理员。 |开始访问审阅，确定不再需要访问的用户。 |
| **全局管理员过多** |存在的全局管理员数量多于建议数量。 |如果你有大量全局管理员，用户获取的权限可能超出需求。 将用户移到较少特权的角色，或使某些用户符合角色条件，而非永久分配。 |

## <a name="configure-security-alert-settings"></a>配置安全警报设置
可以在 PIM 中自定义某些安全警报，以处理你的环境和安全目标。 按照以下步骤访问设置边栏选项卡：

1. 登录 [Azure 门户](https://portal.azure.com/)，然后从仪表板中选择“Azure AD Privileged Identity Management”磁贴。
2. 依次选择“托管的特权角色” > “设置” > “警报设置”。
   
    ![导航到安全警报设置][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>“角色激活过于频繁”警报
如果用户在指定期限内多次激活同一特权角色，将触发此警报。 可以同时配置时间段和激活次数。

* **激活续订时间范围**：以天、小时、分钟和秒为单位指定要用于跟踪可疑续订的时间段。
* **激活续订次数**：在所选时间范围内指定你认为值得发送警报的激活次数（范围介于 2 到 100）。 可通过移动滑块或在文本框中键入数字更改此设置。

### <a name="there-are-too-many-global-administrators-alert"></a>“全局管理员过多”警报
如果满足两个不同的条件并且你可以同时配置这两个条件，PIM 将触发此警报。 首先，需要达到全局管理员的某个特定阈值。 其次，总角色分配的特定百分比必须是全局管理员。 如果只满足其中一个度量，不会显示该警报。  

* **最少全局管理员数**：指定你认为不安全的全局管理员数（范围介于 2 到 100）。
* **全局管理员百分比**：指定在你的环境中不安全的管理员（属于全局管理员）百分比（范围介于 0% 到 100%）。

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>“管理员不使用其特权角色”警报
如果用户在一定时间过后没有激活角色，将触发此警报。

* **天数**：指定用户不激活角色的天数（范围介于 0 到 100）.

## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png



<!--HONumber=Dec16_HO5-->


