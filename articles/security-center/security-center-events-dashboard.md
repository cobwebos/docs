---
title: "在 Azure 安全中心监视和处理安全事件 | Microsoft Docs"
description: "了解如何使用安全中心的事件仪表板查看 Azure VM 和非 Azure 计算机中的安全事件。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: terrylan
ms.openlocfilehash: 367067874b167268bd690a9e0b55412e92e08122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>在 Azure 安全中心监视和处理安全事件
事件仪表板提供随着时间推移收集到的安全事件数的概述，以及可能需要注意的重要事件列表。  

> [!NOTE]
> 若要使用此功能，工作区必须运行 Log Analytics 版本 2 并且位于安全中心的标准层上。 请参阅安全中心[定价页](security-center-pricing.md)，了解有关标准层的详细信息。
>
>

## <a name="what-is-a-security-event"></a>什么是安全事件？
安全中心使用 Microsoft Monitoring Agent 收集计算机中各种与安全有关的配置和事件，并将这些事件存储在工作区中。 以下是此类数据的示例：操作系统日志（Windows 事件日志）、正在运行的进程和来自已集成安全中心的安全解决方案的事件。 Microsoft Monitoring Agent 还将故障转储文件复制到工作区。

## <a name="events-processed-dashboard"></a>“已处理事件”仪表板
可从安全中心主菜单或安全中心“概述”边栏选项卡访问“事件”仪表板。  

![“已处理事件”仪表板][1]

“安全中心”下的“事件”磁贴将显示从 Azure VM 和非 Azure 计算机流入安全中心的事件数。

“事件仪表板”提供随着时间推移已处理的事件数的概述，以及事件列表。

 ![仪表板][2]

 仪表板的上半部分提供过去一周中处理的所有事件的趋势图。 仪表板的下半部分列出按类型分类的重要事件和所有事件：

 - “重要事件”包括安全中心提供的事件查询以及你创建和添加的事件查询。 还可通过此仪表板快速查看每个重要事件的计数。
 - “所有事件(按类型分类)”显示收到的事件类型和每个类型的计数。 以下是事件类型示例：SecurityEvent、CommonSecurityLog、WindowsFirewall 和 W3CIISLog。

> [!NOTE]
> 显著事件包括 [Web 基线评估](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment)。 Web 基线评估的目的是找出可能有漏洞的 Web 服务器设置。

## <a name="view-processed-event-details"></a>查看已处理事件的详细信息
1. 在“安全中心”主菜单下，选择“事件”。
2. 此时，“事件仪表板”工作区选择器可能会打开。 如果只有一个工作区，将不会出现此工作区选择器。 如果有多个工作区，则需要选择一个工作区，以查看它的已处理事件详细信息。 如果有多个工作区，则从列表中选择一个工作区。

  ![工作区列表][3]

3. 此时，“事件仪表板”打开，显示选定工作区的事件详细信息。 可以查看重要事件和所有事件（按类型分类）。  在此示例中，我们选择了“重要事件”。

  ![重要事件][4]

4. 可以通过选择一个事件类型来在此工作区中查询更多数据。 在此示例中，我们选择了“SecurityEvent”。

  ![选择一个事件类型][5]

5. “日志搜索”将打开，其中包含关于此事件类型的更多详细信息。

  ![日志搜索][6]

## <a name="add-a-notable-event"></a>添加重要事件
安全中心将提供现成的重要事件。 可以使用 [Log Analytics 查询语言](../log-analytics/log-analytics-search-reference.md)并根据自己的查询添加重要事件。 将返回到“事件仪表板”，以添加显著事件。

1. 选择“添加重要事件”。

  ![添加重要事件][7]

2. “添加自定义重要事件”将打开。  在“显示名称”下，输入重要事件的名称。 在“搜索查询”下，输入针对此事件的查询。

  ![输入查询][8]

4. 选择“确定”。

## <a name="update-your-workspace-for-events-processing"></a>更新工作区以处理事件
工作区必须运行 Log Analytics 版本 2，并且必须位于安全中心的标准层上才能使用安全中心中的事件处理功能。 “事件仪表板”工作区选择器确定了不符合这些要求的工作区。

![工作区不符合要求][9]

如果工作区行：

- 包含“需要更新” - 需要将工作区更新到 Log Analytics 版本 2
- 包含“升级计划” – 需要将工作区升级到安全中心的标准层
- 为空 - 你的工作区符合要求，选择一个工作区后即会转向仪表板

> [!NOTE]
> 在“事件仪表板”下，“事件数”列指明每个工作区中的事件数量。  一些工作区的此列为空，因为应用到该工作区的是安全中心的免费层。 在免费层下，安全中心会收集事件，但不会将事件保存到 Log Analytics 中，因此这些事件在仪表板中不可用。
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>将工作区更新到 Log Analytics 版本 2
1. 选择“需要更新”的工作区。
2. “搜索升级”将打开。 选择“立即升级”。

  ![立即升级][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>升级到安全中心的标准层
1. 选择包含“升级计划”的工作区。
2. “事件仪表板”将打开。 选择“试用事件仪表板”。

  ![试用仪表板][11]

3. 在“载入高级安全”下，选择要升级的工作区。
4. 在“定价”下，选择“标准层”。
5. 选择“保存”。

  ![升级到标准层][12]

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用安全中心的“事件仪表板”。 若要详细了解此仪表板的工作原理及如何编写自己的事件查询，请参阅：

- [什么是 Log Analytics？](../log-analytics/log-analytics-overview.md) – Log Analytics 概述
- [了解 Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-search-new.md) - 介绍日志搜索在 Log Analytics 中的用法以及创建日志搜索之前应了解的概念
- [Log Analytics 搜索参考](../log-analytics/log-analytics-search-reference.md) – 了解如何使用 Log Analytics 中的查询语言编写自己的事件查询

若要了解有关安全中心的详细信息，请参阅：

- [安全中心概述](security-center-intro.md) – 介绍安全中心的主要功能

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
