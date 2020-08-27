---
title: 规划 Azure 维护事件
description: 了解如何为在 Azure SQL 数据库和 Azure SQL 托管实例中执行计划内维护事件做准备。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 08/25/2020
ms.openlocfilehash: 4c7b78f14602632068a19d520aeeb940b543be61
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948209"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>规划在 Azure SQL 数据库和 Azure SQL 托管实例中的 Azure 维护事件
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

了解如何为在 Azure SQL 数据库和 Azure SQL 托管实例中的数据库上执行计划内维护事件做准备。

## <a name="what-is-a-planned-maintenance-event"></a>什么是计划内维护事件？

为了保持 Azure SQL 数据库和 Azure SQL 托管实例服务的安全、合规性、稳定性和高性能，几乎可以通过服务组件来不断地进行更新。 由于新式可靠的服务体系结构和 [热修补](https://aka.ms/azuresqlhotpatching)等创新技术，大多数更新在服务可用性方面都是完全透明和非有影响力的。 尽管如此，少量的更新会导致服务中断短暂并且需要特殊处理。 

Azure SQL 数据库和 Azure SQL 托管实例为每个数据库维护了一组正常运营所需的最低数量的数据库副本，其中一个副本是主副本。 主副本在任何时间都必须处于联机运行提供服务的状态，且至少要有一个辅助副本处于正常可用的状态。 在计划内维护期间，所维护的数据库副本将进入脱机状态，一次脱机一个，目的是要有一个能够响应的主副本和至少一个辅助副本处于联机状态，确保不发生客户端停机。 当主副本需要进入脱机状态时，将启动重新配置/故障转移进程，其间，会有一个辅助副本变为新的主副本。  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>计划内维护事件期间会发生什么

根据维护事件开始时主副本和辅助副本的星座，维护事件可以产生单个或多个故障转移。 平均情况下，每个计划内维护事件发生1.7 故障转移。 重新配置/故障转移通常在 30 秒内完成。 平均 8 秒。 如果已连接，则应用程序必须重新连接到数据库的新主副本。 如果尝试在新的主副本处于联机状态的情况下重新配置数据库，则会出现错误40613， (数据库不可用) ： *服务器 "{servername}" 上的数据库 "{databasename}" 当前不可用。请稍后重试连接 "。* 如果数据库有一个长时间运行的查询，重新配置期间此查询会中断，需要重新启动。

## <a name="how-to-simulate-a-planned-maintenance-event"></a>如何模拟计划内维护事件

在部署到生产环境之前，确保客户端应用程序能够恢复维护事件，从而有助于降低应用程序故障的风险，并将对最终用户造成应用程序可用性的影响。 可以通过 PowerShell、CLI 或 REST API [启动手动故障转移](https://aka.ms/mifailover-techblog) ，在计划内维护事件期间测试客户端应用程序的行为。 它将生成与使主副本脱机的维护事件相同的行为。

## <a name="retry-logic"></a>重试逻辑

连接到云数据库服务的任何客户端生产应用程序均应实现一个可靠的连接[重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。 这将有助于使故障转移对最终用户透明，或至少最大程度地减少负面影响。

## <a name="resource-health"></a>资源运行状况

如果数据库发生登录失败的情况，请在 [Azure 门户](https://portal.azure.com)的[资源运行状况](../../service-health/resource-health-overview.md#get-started)窗口中查看当前状态。 运行状况历史记录部分包含每个事件（如果有）的停机原因。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure SQL 数据库和 Azure SQL 托管实例的[资源运行状况](resource-health-to-troubleshoot-connectivity.md)。
- 有关重试逻辑的详细信息，请参阅[针对暂时性错误的重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。
