---
title: Synapse SQL 池的维护计划
description: 维护计划使客户能够围绕 Azure Synapse Analytics 用来推出新功能、升级和修补程序的必要计划性维护事件进行规划。
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 1c5bc4400e99fb1c24e321e623aaee523b9c7383
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85210977"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>使用维护计划管理服务更新和维护

此维护计划功能可集成服务运行状况计划内维护通知、资源运行状况检查监视器，以及针对 Azure Synapse Analytics 中的 Synapse SQL 池（数据仓库）的维护计划服务。

应使用维护计划选择一个方便接收新功能、升级和修补程序的时间范围。 你将需要选择 7 天内的主要和辅助维护时段，每个时段必须在单独的日期范围内。

例如，可以这样计划：主要时段为星期六 22:00 到星期日 01:00，辅助时段为星期三 19:00 到 22:00。 如果在主要维护时段无法进行维护，它会尝试在辅助维护时段再次进行维护。 在主要时段和辅助时段期间都可能会进行服务维护。 为了确保快速完成所有维护操作，DW400c 和更低的数据仓库层可以在指定的维护时段之外完成维护。

所有新创建的数据仓库实例会在部署期间应用系统定义的维护计划。 部署完成后即可编辑该计划。

虽然维护时段可以是 3 到 8 小时，但这并不意味着数据仓库会在这段时间内脱机。 我们可能会在该时段内的任何时间进行维护，你应该预料到在该时段内会出现一次断开连接的情况，持续时间大约为 5 到 6 分钟，因为服务需将新代码部署到数据仓库。 在维护时段，DW400c 及更低的数据仓库层可能会多次出现短暂的连接丢失情况。 维护开始时，将取消所有活动会话，并回退未提交的事务。 若要尽量减少实例停机时间，请确保数据仓库在选定的维护时限之前没有任何长时间运行的事务。

所有维护操作都会在指定的维护时段内完成，除非我们必须部署时间敏感型更新。 如果在计划维护期间暂停数据仓库，则会在恢复操作期间进行更新。 数据仓库维护完成后，你会立即收到通知。

## <a name="alerts-and-monitoring"></a>警报和监视

与服务运行状况通知和资源运行状况检查监视器的集成可让客户随时了解即将发生的维护活动。 此自动化利用 Azure Monitor。 你可以决定如何接收有关即将进行的维护事件的通知。 此外，可以选择自动化流来管理停机时间，尽量减少操作影响。

我们会在所有维护事件之前提前 24 小时通知，DWC400c 和更低层例外。

> [!NOTE]
> 在我们需要部署时间关键型更新的情况下，提前通知时间可能会显著减少。

如果你提前收到了维护通知，但维护无法在通知中指定的时段进行，你会收到取消通知。 随即会在下一个计划的维护期间继续进行维护。

所有活动维护事件显示在“服务运行状况 - 计划内维护”部分中。  服务运行状况历史记录包括以往事件的完整记录。 可以在活动事件期间通过 Azure 服务运行状况检查门户仪表板监视维护。

### <a name="maintenance-schedule-availability"></a>维护计划可用性

即使维护计划在所选的区域中不可用，也随时可以查看和编辑维护计划。 维护计划在你的区域中可用后，指定的计划会立即对你的 Synapse SQL 池生效。

## <a name="view-a-maintenance-schedule"></a>查看维护计划

默认情况下，所有新创建的数据仓库实例在部署期间会有八小时的主维护时段和辅助维护时段。 如上所述，部署完成后，你可以更改此时段。 在未事先通知的情况下，不会在指定的维护时段外进行维护。

若要查看已应用于 Synapse SQL 池的维护计划，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择要查看的 Synapse SQL 池。
3. 所选的 Synapse SQL 池随即在“概览”边栏选项卡上打开。 应用于该数据仓库的维护计划将显示在“维护计划”下方。 

![概述边栏选项卡](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>更改维护计划

可以在任何时间更新或更改维护计划。 如果所选的实例正在经历活动维护周期，将保存设置。 在下一个确定的维护周期，它们将处于活动状态。 [详细了解](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)在活动维护事件期间监视数据仓库。

## <a name="identifying-the-primary-and-secondary-windows"></a>确定主要和辅助时段

主要和辅助时段必须包含不同的日期范围。 例如，主要时段为星期二到星期四，辅助时段为星期六到星期日。

若要更改 Synapse SQL 池的维护计划，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择要更新的 Synapse SQL 池。 页面将在概述边栏选项卡上打开。
在“概览”边栏选项卡上选择“维护计划摘要”链接，打开维护计划设置的页面。  或者，选择左侧资源菜单中的“维护计划”选项。 

    ![概述边栏选项卡选项](./media/maintenance-scheduling/maintenance-change-option.png)

3. 使用页面顶部的选项确定主要维护时段的首选日期范围。 此选择确定主要时段会出现在工作日还是周末。 所做的选择会更新下拉列表值。
在预览期，某些区域可能尚不支持完整的可用“日期”选项集。 

   ![维护设置边栏选项卡](./media/maintenance-scheduling/maintenance-settings-page.png)

4. 使用下拉列表框选择首选的主要和辅助维护时段：
   - **日**：在所选时段内执行维护的首选日期。
   - **开始时间**：维护时段的首选开始时间。
   - **时间范围**：时间范围的首选持续时间。

   边栏选项卡底部的“计划摘要”区域将根据所选的值更新。 
  
5. 选择“保存”  。 此时会显示一条消息，确认新计划现已处于活动状态。

   如果要在不支持维护计划的区域中保存计划，则会显示以下消息。 当此功能在所选区域中可用时，设置将会保存并且变为活动状态。

   ![有关区域可用性的消息](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>后续步骤

- [详细了解](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)如何使用 Azure Monitor 创建、查看和管理警报。
- [详细了解](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)用于日志警报规则的 Webhook 操作。
- [深入了解](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)创建和管理操作组。
- [详细了解](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure 服务运行状况。
