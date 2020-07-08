---
title: 规划 Azure 维护事件
description: 了解如何为 Azure SQL 数据库和 Azure SQL 托管实例中的计划内维护事件做准备。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 5bdc3eb8c118c19f90ce1fd92ac5ee156719dacd
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987192"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>在 Azure SQL 数据库和 Azure SQL 中规划 Azure 维护事件托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

了解如何在 Azure SQL 数据库和 Azure SQL 托管实例中为数据库的计划内维护事件做准备。

## <a name="what-is-a-planned-maintenance-event"></a>什么是计划内维护事件？

对于每个数据库，Azure SQL 数据库和 Azure SQL 托管实例维护一个副本是主要副本的数据库副本的仲裁。 主副本在任何时候都必须联机维护，并且至少一个辅助副本必须处于正常运行状态。 在计划内维护期间，所维护的数据库副本将进入脱机状态，一次脱机一个，目的是要有一个能够响应的主副本和至少一个辅助副本处于联机状态，确保不发生客户端停机。 当主副本需要进入脱机状态时，将启动重新配置/故障转移进程，其间，会有一个辅助副本变为新的主副本。  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>计划内维护事件期间会发生什么

重新计算/故障转移通常在30秒内完成。 平均为8秒。 如果应用程序处于已连接状态，则必须重新连接至新的数据库健康主副本。 如果尝试在新的主副本处于联机状态的情况下重新配置数据库，则会出现错误40613（数据库不可用）：当前不支持服务器 "{servername}" 上的数据库 "{databasename}"。 请稍后重试连接”错误。 如果数据库具有长时间运行的查询，则此查询将在重新配置期间中断，并需要重新启动。

## <a name="retry-logic"></a>重试逻辑

连接到云数据库服务的任何客户端生产应用程序均应实现一个可靠的连接[重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。 这有助于缓解此类状况，通常可以让最终用户清晰地看到错误。

## <a name="frequency"></a>频率

每个月平均会发生 1.7 个计划内维护事件。

## <a name="resource-health"></a>资源运行状况

如果数据库遇到登录失败，请在[Azure 门户](https://portal.azure.com)中检查当前状态的 "[资源运行状况](../../service-health/resource-health-overview.md#get-started)" 窗口。 运行状况历史记录部分包含每个事件（如果有）的停机原因。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure SQL 数据库和 Azure SQL 托管实例的[资源运行状况](resource-health-to-troubleshoot-connectivity.md)。
- 有关重试逻辑的详细信息，请参阅[暂时性错误的重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。
