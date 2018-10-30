---
title: Azure 维护计划（预览版）| Microsoft Docs
description: 维护计划使客户能够围绕 Azure SQL 数据仓库服务用于推出新功能、升级和修补程序的必要计划性维护事件进行规划。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: c2ed79673af3563ae62f516057a174770cda99e9
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427856"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>使用维护计划管理服务更新和维护

Azure SQL 数据仓库维护计划目前以预览版提供。 此功能可集成服务运行状况计划内维护通知、资源运行状况检查监视器和 Azure SQL 数据仓库维护计划服务。

使用维护计划可以选择一个方便接收新功能、升级和修补程序的时间范围。 请选择 7 天内的主要和辅助维护时段。 例如，主要时段为星期六 22:00 到星期日 01:00，辅助时段为星期三 19:00 到 22:00。 如果 SQL 数据仓库在主要维护时段无法执行维护，则它会尝试在辅助维护时段再次执行维护。

所有新创建的 Azure SQL 数据仓库实例将在部署期间应用系统定义的维护计划。 部署完成后即可编辑该计划。

每个维护时段可以是 3 到 8 小时。 可在该时段的任何时间执行维护。 由于服务要将新代码部署到数据仓库，因此，短暂性的连接断开属于预料之内的问题。 

在此功能的预览期，可以在单独的日期范围内指定主要时段和辅助时段。 所有维护操作应在计划的维护时段内完成。 在未事先通知的情况下，不会在指定的维护时段外进行维护。 如果在计划维护期间暂停数据仓库，则会在恢复操作期间进行更新。  


## <a name="alerts-and-monitoring"></a>警报和监视

与服务运行状况通知和资源运行状况检查监视器的集成可让客户随时了解即将发生的维护活动。 新的自动化利用 Azure Monitor。 你可以决定如何接收有关即将进行的维护事件的通知。 此外，请确定哪些自动化流可以帮助你管理停机时间，以及尽量减少对操作的影响。

在执行所有维护事件之前，会提前 24 小时发送通知。 若要尽量减少实例停机时间，请确保数据仓库在选定的维护时限之前没有任何长时间运行的事务。 维护开始时，将取消所有活动会话。 未提交的事务将会回滚，数据仓库将遇到短暂的连接断开。 针对数据仓库的维护完成后，你会立即收到通知。 

如果提前收到了维护通知，但 SQL 数据仓库无法在此期间执行维护，则你会收到取消通知。 随即会在下一个计划的维护期间继续进行维护。
 
所有活动维护事件显示在“服务运行状况 - 计划内维护”部分中。 服务运行状况历史记录包括以往事件的完整记录。 可以在活动事件期间通过 Azure 服务运行状况检查门户仪表板监视维护。

### <a name="maintenance-schedule-availability"></a>维护计划可用性

即使维护计划在所选的区域中不可用，也随时可以查看和编辑维护计划。 维护计划在你的区域中可用后，指定的计划将立即针对你的数据仓库生效。


## <a name="next-steps"></a>后续步骤

- [详细了解](viewing-maintenance-schedule.md)如何查看维护计划。 
- [详细了解](changing-maintenance-schedule.md)如何更改维护计划。
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)如何使用 Azure Monitor 创建、查看和管理警报。
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)用于日志警报规则的 Webhook 操作。
- [详细了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服务运行状况。







