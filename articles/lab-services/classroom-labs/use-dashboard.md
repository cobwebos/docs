---
title: 在 Azure 实验室服务中使用教室实验室的仪表板 |微软文档
description: 了解如何在 Azure 实验室服务中使用教室实验室的仪表板。
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538934"
---
# <a name="dashboard-for-classroom-labs"></a>教室实验室仪表板
本文介绍了 Azure 实验室服务中教室实验室的仪表板视图。 

![仪表板](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>成本和计费磁贴
此磁贴提供以下成本估算详细信息：

| 设置 | “值” | 
| ------- | ----- | 
| 配额时数 | 用户可以在计划工时之外使用 VM 的最大小时数。 |
| 预定时间 | 将根据实验室中设置的计划发生的小时数。 仅当所有计划事件都设置了从/到日期时，此值才可用。 |
| 小时/用户 | 配额小时和计划工时的总和。 |
| 最大用户数 | 基于要声明的所有虚拟机，实验室中的最大用户数。 |
| 小时 x 用户 | 工时/用户乘以用户数。 |
| 调整后的配额 | 添加到特定用户的配额小时数的总和。 |
| 总时数 = $/小时 | 基于所选 VM 大小每小时的成本。 这是基于正常支付，你去价格。 |
| 估计费用总额 | 这是此实验室基于当前设置的最高价格。 |

## <a name="template-tile"></a>模板磁贴
有关此磁贴的以下信息：

- 创建模板的日期 
- 模板上次发布的日期 

它还具有一个链接，用于导航到**模板**页面，您可以在该页面[管理类的模板 VM。](how-to-create-manage-template.md) 

## <a name="virtual-machine-pool-tile"></a>虚拟机池磁贴

有关此磁贴的以下信息：

- 分配给学生（用户）的虚拟机数
- 尚未分配给学生的虚拟机数

它还具有一个链接，用于导航到**虚拟机池**页面，您可以在该页面[管理实验室中的虚拟机池](how-to-set-virtual-machine-passwords.md)。 

## <a name="users-tile"></a>用户磁贴

有关此磁贴的以下信息：

- 注册到类的用户数
- 添加到实验室但未注册到类的用户数 

它还具有一个链接，用于导航到 **"用户"** 页面，您可以在该页面[管理实验室的用户](how-to-configure-student-usage.md)。 

## <a name="schedules-tile"></a>计划磁贴
在磁贴上可以看到实验室的当前计划事件。 它还有一个链接，以导航到 **"计划"** 页，您可以在其中[创建和管理计划](how-to-create-schedules.md)。 磁贴仅显示两个计划事件的详细信息以及实验室的剩余计划事件数。 

![计划事件](../media/use-dashboard/scheduled-events.png)

