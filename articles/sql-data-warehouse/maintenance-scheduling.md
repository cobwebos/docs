---
title: Azure 维护计划（预览版）| Microsoft Docs
description: 维护计划可让客户围绕 Azure SQL 数据仓库服务用于推出新功能、升级和修补程序的必要计划维护事件进行规划。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 85e8327d1cac17059b2e91b1ea21becc23002c8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228072"
---
# <a name="using-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>使用维护计划管理服务更新和维护

Azure SQL 数据仓库维护计划现在处于预览状态。 这一新功能可无缝集成服务运行状况计划内维护通知、资源运行状况检查监视器和 Azure SQL 数据仓库维护计划服务。

维护计划可让你在方便接收新功能、升级和修补程序时计划时间范围。 客户将在 7 天内选择主维护时段和辅助维护时段，即，星期六 22:00 - 星期日 01:00（主维护时段）和星期三 19:00-22:00（辅助维护时段）。 如果无法在主维护时段内执行维护，我们将在辅助维护时段内重新尝试。

所有新创建的 Azure SQL 数据仓库实例将在部署期间应用系统定义的维护计划。 部署完成后即可编辑该计划。

每个维护时段可以介于 3 小时和 8 小时之间，其中 3 小时是当前最短的可用选项。 可以在时段内的任何时间进行维护，并且应该预料到短暂丢失连接的原因是该服务将新代码部署到你的数据仓库。 

在功能预览期间，我们要求客户在单独的日期范围内标识其主时段和辅助时段。   
应在计划的维护时段内完成所有维护操作且在未事先通知的情况下不会在指定的维护时段外进行维护。 如果在计划维护期间暂停数据仓库，则会在恢复操作期间进行更新。  


## <a name="alerts-and-monitoring"></a>警报和监视

与服务运行状况通知和资源运行状况检查监视器的无缝集成可让客户随时了解即将发生的维护活动。 新的自动化利用 Azure Monitor，可让客户确定如何了解即将发生的维护事件以及应触发哪些自动化流来管理停机时间并最大程度地减少对其操作的影响。

所有维护事件都会提前 24 小时发送通知。 若要最大程度地缩短实例停机时间，应确保在所选维护期间的起始时间之前数据仓库上不再有正在运行的事务。 维护启动将要取消的所有活动会话时，将回滚未提交的事务，并且你的数据仓库将短暂丢失连接。 数据仓库上完成维护后会立即收到通知。 

如果提前收到了将进行维护的通知，但我们无法在此期间执行维护，则将收到取消通知。 随即会在下一个计划的维护期间继续进行维护。
 
所有活动维护事件都将显示在“服务运行状况 - 计划内维护”部分中。 过去事件的完整记录将保留为服务运行状况历史记录的一部分。 可以在活动事件期间通过 Azure 服务运行状况检查门户仪表板监视维护。

### <a name="maintenance-schedule-availability"></a>维护计划可用性

即使维护计划在你所选的区域中尚不可用，但仍可以随时查看和编辑维护计划。 维护计划在你的区域中变得可用时，标识的计划将立即在数据仓库上变为活动状态。


## <a name="next-steps"></a>后续步骤

- [详细了解](viewing-maintenance-schedule.md)查看维护计划 
- [详细了解](changing-maintenance-schedule.md)更改维护计划
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)如何使用 Azure Monitor 创建、查看和管理警报
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)用于日志警报规则的 Webhook 操作
- [详细了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服务运行状况







