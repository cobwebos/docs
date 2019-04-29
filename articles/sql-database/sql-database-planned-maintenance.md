---
title: 规划 Azure 维护事件 - Azure SQL 数据库 | Microsoft Docs
description: 了解如何为针对 Azure SQL 数据库执行计划内维护事件做准备。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 928338a911efae051df7164239dbd19f9317338a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584601"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>规划 Azure 维护事件 - Azure SQL 数据库

了解如何为针对 Azure SQL 数据库执行计划内维护事件做准备。

## <a name="what-is-a-planned-maintenance-event"></a>什么是计划内维护事件

Azure SQL DB 为每个数据库维护了一组正常运营所需的最低数量的数据库副本，其中一个副本是主副本。 主副本在任何时间都必须处于联机运行提供服务的状态，且至少要有一个辅助副本处于正常可用的状态。 在计划内维护期间，所维护的数据库副本将进入脱机状态，一次脱机一个，目的是要有一个能够响应的主副本和至少一个辅助副本处于联机状态，确保不发生客户端停机。 当主副本需要进入脱机状态时，将启动重新配置/故障转移进程，其间，会有一个辅助副本变为新的主副本。  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>计划内维护事件期间会发生什么

重新配置/故障转移进程通常在 30 秒内完成 – 平均时长为 8 秒。 如果应用程序处于已连接状态，则必须重新连接至新的数据库健康主副本。 如果在进行连接时数据库正在进行重新配置，且新的主副本尚未处于联机状态，会显示错误 40613（数据库不可用）：“服务器 '{服务器名}' 上的数据库 '{数据库名}' 当前不可用。 请稍后重试连接”错误。” 如果数据库有一个长时间运行的查询，重新配置期间此查询会中断，需要重新启动。

## <a name="retry-logic"></a>重试逻辑

连接到云数据库服务的任何客户端生产应用程序均应实现一个可靠的连接[重试逻辑](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)。 这有助于缓解此类状况，通常可以让最终用户清晰地看到错误。

## <a name="frequency"></a>频率

每个月平均会发生 1.7 个计划内维护事件。

## <a name="resource-health"></a>资源运行状况

如果 SQL 数据库发生登录失败的情况，请在 [Azure 门户](https://portal.azure.com)的[资源运行状况](../service-health/resource-health-overview.md#getting-started)窗口中查看当前状态。 运行状况历史记录部分包含每个事件（如果有）的停机原因。


## <a name="next-steps"></a>后续步骤

- 详细了解 SQL 数据库的[资源运行状况](sql-database-resource-health.md)
- 有关重试逻辑的详细信息，请参阅[针对暂时性错误的重试逻辑](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
