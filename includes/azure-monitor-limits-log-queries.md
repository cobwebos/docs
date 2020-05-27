---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82072631"
---
### <a name="general-query-limits"></a>一般查询限制

| 限制 | 说明 |
|:---|:---|
| 查询语言 | Azure Monitor 使用与 Azure 数据资源管理器相同的 [Kusto 查询语言](/azure/kusto/query/)。 有关 Azure Monitor 中不支持的 KQL 语言元素，请参阅 [Azure Monitor 日志查询语言差异](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 区域 | 当数据跨多个 Azure 区域中的 Log Analytics 工作区时，日志查询可能会遇到过多的开销。 有关详细信息，请参阅[查询限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
| 跨资源查询 | 单个查询中的 Application Insights 资源和 Log Analytics 工作区的最大数量限制为 100。<br>视图设计器不支持跨资源查询。<br>新的 scheduledQueryRules API 支持日志警报中的跨资源查询。<br>有关详细信息，请参阅[跨资源查询限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |

### <a name="user-query-throttling"></a>用户查询限制
Azure Monitor 具有多个限制，可防止用户发送过多的查询。 此类行为可能会重载系统后端资源，并危害服务的响应能力。 以下限制旨在防止客户中断，并确保服务级别一致。 用户限制和限制旨在仅影响极端使用方案，不应与典型用法相关。


| 度量 | 每用户限制 | 说明 |
|:---|:---|:---|
| 并发查询 | 5 | 如果已经为用户运行了5个查询，则会在每个用户的并发队列中放置任何新的查询。 当其中一个正在运行的查询结束时，将从队列中提取下一个查询并开始。 这不包括来自警报规则的查询。
| 并发队列中的时间 | 2.5 分钟 | 如果查询在队列中的等待时间超过2.5 分钟，则它将通过代码429的 HTTP 错误响应终止。 |
| 并发队列中的查询总数 | 40 | 队列中的查询数达到40后，任何其他查询将被拒绝，并出现 HTTP 错误代码429。 此数字是对可同时运行的5个查询的补充。 |
| 查询速率 | 每30秒200个查询 | 这是单个用户可以向所有工作区提交查询的总速率。  此限制适用于可视化部件（如 Azure 仪表板和 Log Analytics 工作区摘要页）启动的编程查询或查询。 |

- 优化查询，如[在 Azure Monitor 中优化日志查询](../articles/azure-monitor/log-query/query-optimization.md)中所述。
- 仪表板和工作簿可以在单个视图中包含多个查询，每次加载或刷新查询时都会产生大量的查询。 考虑将它们拆分为按需加载的多个视图。 
- 在 Power BI 中，请考虑仅提取聚合结果而不是原始日志。