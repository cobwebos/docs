---
title: Azure 维护计划（预览版）| Microsoft Docs
description: 维护计划使客户能够围绕 Azure SQL 数据仓库服务用于推出新功能、升级和修补程序的必要计划性维护事件进行规划。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 10/15018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: c1029d5efe0c8e87cd9bc84887826ddf837d32c5
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55456249"
---
# <a name="change-a-maintenance-schedule"></a>更改维护计划 

## <a name="portal"></a>门户
可以在任何时间更新或更改维护计划。 如果所选的实例正在经历活动维护周期，将保存设置。 在下一个确定的维护周期，它们将处于活动状态。 [详细了解](https://docs.microsoft.com/azure/service-health/resource-health-overview)在活动维护事件期间监视数据仓库。 

若要使用维护计划，必须在七天内选择两个维护时段。 每个维护时段可以是 3 到 8 小时。 维护可在维护时段内的任意时间发生，但在未收到事先通知的情况下，不会在维护时段以外发生。 由于服务要将新代码部署到数据仓库，因此，还会出现短暂性的连接断开。 

## <a name="identifying-the-primary-and-secondary-windows"></a>确定主要和辅助时段

主要和辅助时段必须包含不同的日期范围。 例如，主要时段为星期二到星期四，辅助时段为星期六到星期日。

若要更改数据仓库的维护计划，请完成以下步骤：
1.  登录到 [Azure 门户](https://portal.azure.com/)。
2.  选择要更新的数据仓库。 页面将在概述边栏选项卡上打开。 
3.  在概述边栏选项卡上选择“维护计划(预览版)摘要”链接，打开维护计划设置的页面。 或者，选择左侧资源菜单中的“维护计划”选项。  

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

   ![有关区域可用性的消息](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>后续步骤
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)用于日志警报规则的 Webhook 操作。
- [详细了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服务运行状况。


