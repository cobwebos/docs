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
ms.openlocfilehash: 83754842eeb4b5d609596045c11451e898960b9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90064847"
---
### <a name="general-query-limits"></a>一般查询限制

| 限制 | 说明 |
|:---|:---|
| 查询语言 | Azure Monitor 使用与 Azure 数据资源管理器相同的 [Kusto 查询语言](/azure/kusto/query/)。 有关 Azure Monitor 中不支持的 KQL 语言元素，请参阅 [Azure Monitor 日志查询语言差异](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 区域 | 当数据跨多个 Azure 区域中的 Log Analytics 工作区时，日志查询可能会遇到过多的开销。 有关详细信息，请参阅[查询限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
| 跨资源查询 | 单个查询中的 Application Insights 资源和 Log Analytics 工作区的最大数量限制为 100。<br>视图设计器不支持跨资源查询。<br>新的 scheduledQueryRules API 支持日志警报中的跨资源查询。<br>有关详细信息，请参阅[跨资源查询限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |

### <a name="user-query-throttling"></a>用户查询限制
Azure Monitor 具有多个限制，可防止用户发送过多的查询。 这种行为可能会重载系统后端资源，并危害服务响应能力。 以下限制旨在防止客户中断，并确保服务级别一致。 用户限制的设计决定其仅影响极端使用方案，不应与典型使用情况相关。


| 度量 | 每用户限制 | 说明 |
|:---|:---|:---|
| 并发查询 | 5 | 如果已经为用户运行了 5 个查询，则任何新查询都将被放入按用户的并发队列。 当其中一个正在运行的查询结束时，从队列中拉取下一个查询并启动它。 这不包括来自警报规则的查询。
| 并发队列中的时间 | 3 分钟 | 如果查询在队列中等待超过 3 分钟而未启动，将终止该查询并发出包含代码 429 的 HTTP 错误响应。 |
| 并发队列中的查询总数 | 200 | 队列中的查询数达到 200 后，将拒绝其他任何查询，并发出 HTTP 错误代码 429。 这一数字不包含可同时运行的 5 个查询。 |
| 查询速率 | 每 30 秒 200 个查询 | 这是单个用户可以向所有工作区提交查询的整体速率。  此限制适用于编程查询或由可视化部件（如 Azure 仪表板和 Log Analytics 工作区摘要页）启动的查询。 |

- 优化查询，如[在 Azure Monitor 中优化日志查询](../articles/azure-monitor/log-query/query-optimization.md)中所述。
- 仪表板和工作簿可以在单个视图中包含多个查询，每次加载或刷新视图时都会产生大量的查询。 请考虑将它们拆分为按需加载的多个视图。 
- 在 Power BI 中，考虑仅提取聚合结果而不提取原始日志。
