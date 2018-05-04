---
title: 计划内维护 - Azure SQL 数据仓库 | Microsoft Docs
description: 了解如何为针对 Azure SQL 数据仓库执行计划内维护事件做准备。
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>计划对 Azure SQL 数据仓库的维护

了解如何为针对 Azure SQL 数据仓库执行计划内维护事件做准备。

## <a name="what-is-a-planned-maintenance-event"></a>什么是计划内维护事件？
计划内维护事件是数据仓库需要脱机以便执行维护操作的时间窗口。 这些事件是应用服务升级、新功能或修补程序的机会。 

## <a name="frequency"></a>频率
平均而言，每个月至少发生一次计划内维护事件。 

## <a name="notifications-and-downtime"></a>通知和停机时间
每次发生计划内维护事件之前你都会收到通知。 维护事件会取消所有正在运行的查询并使数据仓库脱机。 每个数据仓库的预计停机时间大约为 30 分钟。 维护完成后，应当会收到通知。 

## <a name="setting-up-alerts"></a>设置警报

建议使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) 来设置计划内维护日志警报。 警报可以帮助你计划所需的维护，以便最大限度地降低对你的业务的影响。 

若要设置通知，请使用这些[日志警报说明](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)。 

## <a name="next-steps"></a>后续步骤
有关监视的详细信息，请参阅[监视工作负荷](sql-data-warehouse-manage-monitor.md)。
