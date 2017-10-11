---
title: "如何在 Azure AD Privileged Identity Management 中使用审核日志 | Microsoft 文档"
description: "了解如何在 Azure Privileged Identity Management 扩展中使用审核日志。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 5d13a6dd-1fcb-4e76-82fb-cb2f4f0e4357
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 7d9a5255a64d46c1388d328a606b3f297d61262b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="using-the-audit-log-in-pim"></a>在 PIM 中使用审核日志
可以使用 Privileged Identity Management (PIM) 审核日志查看给定时间段内的所有用户分配和激活。 如果想要在租户中查看活动的完整审核历史记录（包括管理员、最终用户和同步活动），可以使用 [Azure Active Directory 访问和使用情况报告。](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>导航到审核日志
从 [Azure 门户](https://portal.azure.com)仪表板中，选择“Azure AD Privileged Identity Management”应用。 在此处，通过在 PIM 仪表板中依次单击“管理特权角色” > “审核历史记录”访问审核日志。

## <a name="the-audit-log-graph"></a>审核日志图
可以使用审核日志在折线图中查看总激活数、每日最大激活数以及每日平均激活数。  如果审核历史记录中存在多个角色，还可以按角色筛选数据。

使用“时间”、“操作”和“角色”按钮对日志进行排序。

## <a name="the-audit-log-list"></a>审核日志列表
审核日志列表中的各列如下：

* **请求者** - 已请求角色激活或更改的用户。  如果该值为“Azure 系统”，查看 Azure 审核日志获取详细信息。
* **用户** - 正在激活或已分配给角色的用户。
* **角色** - 由用户分配或激活的角色。
* **操作** - 请求者所采取的操作。 这可能包括分配、取消分配、激活或取消激活。
* **时间** - 发生操作的时间。
* **原因** - 如果在激活期间向原因字段中输入了任何文本，该文本会显示在此处。
* **过期** - 仅与激活角色相关。

## <a name="filter-the-audit-log"></a>筛选审核日志
可以通过单击“筛选”按钮筛选显示在审核日志中的信息。  将显示“更新图表参数边栏选项卡”。

设置筛选器后，单击“更新”筛选日志中的数据。  如果数据未立即显示，请刷新该页面。

### <a name="change-the-date-range"></a>更改数据范围
使用“今天”、“上一周”、“上个月”或“自定义”按钮更改审核日志的时间范围。

选择“自定义”按钮时，会获得“从”日期字段和“至”日期字段，可为日志指定日期范围。  可以采用 YYYY/MM/DD 格式输入日期，也可以单击“日历”图标并从日历中选择日期。

### <a name="change-the-roles-included-in-the-log"></a>更改日志中包含的角色
选中或取消选中每个角色旁边的“角色”复选框，以便在日志中包含或排除该角色。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

