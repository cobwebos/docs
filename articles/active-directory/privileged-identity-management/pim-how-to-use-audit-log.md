---
title: 在 PIM 中查看 Azure AD 目录角色的审核历史记录 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中查看 Azure AD 目录角色的审核历史记录。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: a4dbce678d5d3eac106363e958636810ea9040bb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180205"
---
# <a name="view-audit-history-for-azure-ad-directory-roles-in-pim"></a>在 PIM 中查看 Azure AD 目录角色的审核历史记录
可以使用 Privileged Identity Management (PIM) 审核历史记录查看给定时间段内所有特权角色的所有用户分配和激活。 如果想要在租户中查看活动的完整审核历史记录（包括管理员、最终用户和同步活动），可以使用 [Azure Active Directory 访问和使用情况报告。](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>导航到审核历史记录
从 [Azure 门户](https://portal.azure.com)仪表板中，选择“Azure AD Privileged Identity Management”应用。 在此处，通过在 PIM 仪表板中依次单击“管理特权角色” > “审核历史记录”访问审核历史记录。

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
可以按操作对数据排序，并查找“已批准的激活”


## <a name="audit-history-graph"></a>审核历史记录图
可以使用审核历史记录在折线图中查看总激活数、每日最大激活数以及每日平均激活数。  如果审核历史记录中存在多个角色，还可以按角色筛选数据。

使用“时间”、“操作”和“角色”按钮对历史记录进行排序。

## <a name="audit-history-list"></a>审核历史记录列表
审核历史记录列表中的各列如下：

* **请求者** - 已请求角色激活或更改的用户。  如果该值为“Azure 系统”，请查看 Azure 审核历史记录获取详细信息。
* **用户** - 正在激活或已分配给角色的用户。
* **角色** - 由用户分配或激活的角色。
* **操作** - 请求者所采取的操作。 这可能包括分配、取消分配、激活或取消激活。
* **时间** - 发生操作的时间。
* **原因** - 如果在激活期间向原因字段中输入了任何文本，该文本会显示在此处。
* **过期** - 仅与激活角色相关。

## <a name="filter-audit-history"></a>筛选审核历史记录
可以通过单击“筛选”按钮筛选显示在审核历史记录中的信息。  将显示“更新图表参数边栏选项卡”。

设置筛选器后，单击“更新”筛选历史记录中的数据。  如果数据未立即显示，请刷新该页面。

### <a name="change-the-date-range"></a>更改数据范围
使用“今天”、“上一周”、“上个月”或“自定义”按钮更改审核历史记录的时间范围。

选择“自定义”按钮时，会获得“从”日期字段和“至”日期字段，可为历史记录指定日期范围。  可以采用 YYYY/MM/DD 格式输入日期，也可以单击“日历”图标并从日历中选择日期。

### <a name="change-the-roles-included-in-the-history"></a>更改历史记录中包含的角色
选中或取消选中每个角色旁边的“角色”复选框，以便在历史记录中包含或排除该角色。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤

- [在 PIM 中查看 Azure 资源角色的活动和审核历史记录](azure-pim-resource-rbac.md)
