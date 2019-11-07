---
title: 将仪表板用于 Azure 实验室服务中的教室实验室 |Microsoft Docs
description: 了解如何在 Azure 实验室服务中使用课堂实验室的仪表板。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: d1e34a493b747383ce479bcad638098b41e59d2b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588182"
---
# <a name="dashboard-for-classroom-labs"></a>用于课堂实验室的仪表板
本文介绍了 Azure 实验室服务中的 "教室实验室" 的仪表板视图。 

![仪表板](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>成本和计费磁贴
此磁贴提供以下成本估算详细信息：

| 设置 | 值 | 
| ------- | ----- | 
| 配额小时数 | 用户可在计划时间之外使用 VM 的最大小时数。 |
| 计划时间 | 根据实验室中设置的计划将产生的小时数。 仅当所有计划事件都设置了 from/to date 时，此值才可用。 |
| 小时/用户 | 配额小时数和计划小时数之和。 |
| 最多用户 | 实验室中基于要声称的所有虚拟机的最大用户数。 |
| 小时 x 用户 | 小时/用户乘以用户数。 |
| 调整的配额 | 添加到特定用户的配额小时数之和。 |
| 总小时数 * $/小时 | 基于所选 VM 大小的每小时成本。 这是根据你的走向价格定期支付的。 |
| 总预估成本 | 这是此实验室基于当前设置的最大价格。 |

## <a name="template-tile"></a>模板磁贴
此磁贴上会显示以下信息：

- 模板的创建日期 
- 模板的上次发布日期 

它还包含一个导航到**模板**页面的链接，你可以在其中管理类的[模板 VM](how-to-create-manage-template.md) 。 

## <a name="virtual-machine-pool-tile"></a>虚拟机池磁贴

此磁贴上会显示以下信息：

- 分配给学生的虚拟机数（用户）
- 尚未分配给学生的虚拟机数

它还包含一个导航到 "**虚拟机池**" 页的链接，你可以在其中管理实验室中[的虚拟机池](how-to-set-virtual-machine-passwords.md)。 

## <a name="users-tile"></a>用户磁贴

此磁贴上会显示以下信息：

- 注册到类的用户数
- 添加到实验室但未注册到类的用户数 

它还包含一个导航到 "**用户**" 页的链接，你可以在其中管理实验室的[用户](how-to-configure-student-usage.md)。 

## <a name="schedules-tile"></a>计划磁贴
你将在磁贴上看到实验室的当前计划事件。 它还包含一个导航到 "**计划**" 页的链接，你可以在其中[创建和管理计划](how-to-create-schedules.md)。 该磁贴仅显示两个计划事件的详细信息和实验室的剩余计划事件数。 

![计划事件](../media/use-dashboard/scheduled-events.png)

