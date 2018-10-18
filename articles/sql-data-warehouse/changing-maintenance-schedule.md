---
title: Azure 维护计划（预览版）| Microsoft Docs
description: 维护计划可让客户围绕 Azure SQL 数据仓库服务用于推出新功能、升级和修补程序的必要计划维护事件进行规划。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228067"
---
# <a name="change-a-maintenance-schedule"></a>更改维护计划 

## <a name="portal"></a>门户
可以在任何时间更新或更改维护计划。 但是，如果所选实例当前正在进行活动维护周期，则设置将被保存并且仅在下一个标识的维护期间变为活动状态。 [详细了解](https://docs.microsoft.com/azure/service-health/resource-health-overview)在活动维护事件期间监视数据仓库。 

在预览版中，我们将要求在 7 天期间选择两个维护时段。 每个维护时段可以介于 3 小时和 8 小时之间，3 小时目前是最短可用选项。 维护可能会在标识的维护时段内随时发生，但不会发生在标识的时间段之外，并且会事先通知，当服务将新代码部署到你的数据仓库时，还会遇到短暂的连接丢失。 

## <a name="identifying-the-primary-and-secondary-windows"></a>标识主要和辅助时段

必须在不同的日期范围内标识主要和辅助时段（即，主要时段(星期二 – 星期四)，辅助窗口(星期六 – 星期日)）

完成以下步骤，以更改已应用于门户中的数据仓库的维护计划。
1.  登录到 [Azure 门户](https://portal.azure.com/)。
2.  选择要更新的数据仓库。 页面将在概述边栏选项卡上打开。 
3.  通过单击概述边栏选项卡中的维护计划（预览版）摘要链接或通过左侧资源菜单中的维护计划选项，可以访问“维护计划设置”页面。  

    ![概述边栏选项卡选项](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. 使用页面顶部的单选按钮，可以确定主要维护时段的首选日期范围。 此选择确定主要时段会出现在工作日还是周末。 你的选择将相应地更新下面的下拉值。 在预览某些区域期间，可能尚不支持完整的可用“日”选项集。 这些值将在未来几个月更新。

   ![维护设置边栏选项卡](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. 使用下面的“日”、“开始时间”和“时间范围”下拉列表选择首选的主要和辅助维护时段。 边栏选项卡底部的计划摘要将基于选择的下拉值更新。

#### <a name="dropdown-options"></a>下拉选项
- 日：在所选时段内执行维护的首选日期。
- 开始时间：首选的维护时段开始时间。
- 时间范围：首选的时间范围持续时间。

  在选择首选的维护时段后，单击“保存”。 将显示一条确认消息，确认你的新计划处于非活动状态。 如果要在尚不支持维护计划的区域中保存计划，则将显示以下消息。 当功能在所选区域中可用时，你的设置将被保存并且变为活动状态。    

    ![在区域 toast 中未处于活动状态](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>后续步骤
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)日志警报规则的 Webhook 操作。
- [详细了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服务运行状况


