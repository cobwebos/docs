---
title: Synapse SQL 池的维护计划
description: 维护计划使客户能够围绕 Azure Synapse Analytics 用于推出新功能、升级和修补程序的必要计划维护事件进行规划。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 418fbd0c1262de889e0c5f318ef8ce7fe519599f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78193252"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>使用维护计划管理服务更新和维护

"维护计划" 功能将 Azure Synapse 分析中的 Synapse SQL 池（数据仓库）的服务运行状况计划内维护通知、资源运行状况检查监视器和维护计划服务集成在一起。 

在方便接收新功能、升级和修补程序时，应使用维护计划来选择时间范围。 你将需要在七天内选择主要维护窗口和辅助维护时段，每个窗口都必须在不同的日期范围内。

例如，你可以将周六22:00 的主窗口安排到星期日01:00，然后将星期三19:00 的辅助窗口计划为22:00。 如果维护不能在主维护时段内执行，它将在辅助维护时段内再次尝试维护。 在主窗口和辅助窗口中，都可以进行服务维护。 为确保快速完成所有维护操作，DW400c 和更低的数据仓库层可以在指定的维护时段之外完成维护。

所有新创建的数据仓库实例都将在部署期间应用系统定义的维护计划。 部署完成后即可编辑该计划。

尽管维护时段可以介于三到8个小时之间，但这并不意味着数据仓库在持续时间内处于脱机状态。 维护可以在该时段内随时进行，并且在该服务将新代码部署到数据仓库时，应会在该 5-6 时间段内出现一次断开连接。 在维护时段，DW400c 和 lower 在不同时间可能会出现多个短暂的连接中断。 当维护开始时，将取消所有活动会话，并且将回滚未提交的事务。 若要尽量减少实例停机时间，请确保数据仓库在选定的维护时限之前没有任何长时间运行的事务。

所有维护操作都应在指定的维护时段内完成，除非需要部署时间敏感更新。 如果在计划维护期间暂停数据仓库，则会在恢复操作期间进行更新。 数据仓库维护完成后，你将立即获得通知。

## <a name="alerts-and-monitoring"></a>警报和监视

与服务运行状况通知和资源运行状况检查监视器的集成可让客户随时了解即将发生的维护活动。 此自动化充分利用 Azure Monitor。 你可以决定如何接收有关即将进行的维护事件的通知。 此外，你还可以选择哪些自动流可帮助你管理停机时间和最大程度地降低运营影响。

24小时提前通知优先于不属于 DWC400c 和更低层的所有维护事件。

> [!NOTE]
> 如果需要部署时间重要更新，高级通知时间可能会显著降低。

如果您收到了需要进行维护的提前通知，但在通知中的时间段内无法执行维护，则您将收到一条取消通知。 随即会在下一个计划的维护期间继续进行维护。

所有活动维护事件显示在“服务运行状况 - 计划内维护”部分中。 服务运行状况历史记录包括以往事件的完整记录。 可以在活动事件期间通过 Azure 服务运行状况检查门户仪表板监视维护。

### <a name="maintenance-schedule-availability"></a>维护计划可用性

即使维护计划在所选的区域中不可用，也随时可以查看和编辑维护计划。 当维护计划在你的区域中可用时，确定的计划将在 Synapse SQL 池上立即变为活动状态。

## <a name="view-a-maintenance-schedule"></a>查看维护计划 

默认情况下，在部署期间，所有新创建的数据仓库实例都有八个小时的主维护时段和辅助维护时段。 如上所述，可以在部署完成后更改 windows。 在未事先通知的情况下，不会在指定的维护时段外进行维护。

若要查看已应用到 Synapse SQL 池的维护计划，请完成以下步骤：

1.  登录 [Azure 门户](https://portal.azure.com/)。
2.  选择要查看的 Synapse SQL 池。 
3.  "概述" 边栏选项卡上会打开所选的 Synapse SQL 池。 应用到数据仓库的维护计划显示在**维护计划**下。

![概述边栏选项卡](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>更改维护计划 

可以在任何时间更新或更改维护计划。 如果所选的实例正在经历活动维护周期，将保存设置。 在下一个确定的维护周期，它们将处于活动状态。 [详细了解](../service-health/resource-health-overview.md)在活动维护事件期间监视数据仓库。 

## <a name="identifying-the-primary-and-secondary-windows"></a>确定主要和辅助时段

主要和辅助时段必须包含不同的日期范围。 例如，主要时段为星期二到星期四，辅助时段为星期六到星期日。

若要更改 Synapse SQL 池的维护计划，请完成以下步骤：
1.  登录 [Azure 门户](https://portal.azure.com/)。
2.  选择要更新的 Synapse SQL 池。 页面将在概述边栏选项卡上打开。 
3.  通过选择 "概述" 边栏选项卡上的 "**维护计划摘要**" 链接，打开 "维护计划设置" 页。 或者，选择左侧资源菜单中的“维护计划”选项。  

    ![概述边栏选项卡选项](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. 使用页面顶部的选项确定主要维护时段的首选日期范围。 此选择确定主要时段会出现在工作日还是周末。 所做的选择会更新下拉列表值。 在预览期，某些区域可能尚不支持完整的可用“日期”选项集。

   ![维护设置边栏选项卡](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. 使用下拉列表框选择首选的主要和辅助维护时段：
   - **日期**：在所选时段内执行维护的首选日期。
   - **开始时间**：维护时段的首选开始时间。
   - **时间范围**：时间范围的首选持续时间。

   边栏选项卡底部的“计划摘要”区域将根据所选的值更新。 
  
6. 选择“保存”。 此时会显示一条消息，确认新计划现已处于活动状态。 

   如果要在不支持维护计划的区域中保存计划，则会显示以下消息。 当此功能在所选区域中可用时，设置将会保存并且变为活动状态。    

   ![有关区域可用性的消息](media/sql-data-warehouse-maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>后续步骤
- [详细了解](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)如何使用 Azure Monitor 创建、查看和管理警报。
- [详细了解](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)用于日志警报规则的 Webhook 操作。
- [深入了解](../monitoring-and-diagnostics/monitoring-action-groups.md)创建和管理操作组。
- [详细了解](../service-health/service-health-overview.md) Azure 服务运行状况。
