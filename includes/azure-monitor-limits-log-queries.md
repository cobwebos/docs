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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072631"
---
### <a name="general-query-limits"></a>常规查询限制

| 限制 | 说明 |
|:---|:---|
| 查询语言 | Azure Monitor 使用与 Azure 数据资源管理器相同的 [Kusto 查询语言](/azure/kusto/query/)。 有关 Azure Monitor 中不支持的 KQL 语言元素，请参阅 [Azure Monitor 日志查询语言差异](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 区域 | 当数据跨多个 Azure 区域中的 Log Analytics 工作区时，日志查询可能会遇到过多的开销。 有关详细信息，请参阅[查询限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
| 跨资源查询 | 单个查询中的 Application Insights 资源和 Log Analytics 工作区的最大数量限制为 100。<br>视图设计器不支持跨资源查询。<br>新的 scheduledQueryRules API 支持日志警报中的跨资源查询。<br>有关详细信息，请参阅[跨资源查询限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |

### <a name="user-query-throttling"></a>用户查询限制
Azure 监视器具有多个限制限制，以防止用户发送过多查询。 此类行为可能会使系统后端资源超载，并危及服务响应能力。 以下限制旨在保护客户免受中断，并确保一致的服务级别。 用户限制和限制设计为仅影响极端使用方案，不应与典型使用情况相关。


| 度量 | 每个用户的限制 | 说明 |
|:---|:---|:---|
| 并发查询 | 5 | 如果用户已经运行了 5 个查询，则任何新查询都放在每个用户并发队列中。 当其中一个正在运行的查询结束时，下一个查询将从队列中拉出并启动。 这不包括来自警报规则的查询。
| 并发队列中的时间 | 2.5 分钟 | 如果查询在队列中存在超过 2.5 分钟而不启动，则它将使用代码 429 的 HTTP 错误响应终止。 |
| 并发队列中查询总数 | 40 | 一旦队列中的查询数达到 40，任何其他查询都将通过 HTTP 错误代码 429 拒绝。 此数字是可同时运行的 5 个查询的补充。 |
| 查询率 | 每 30 秒 200 个查询 | 这是单个用户可以将查询提交到所有工作区的总体速率。  此限制适用于由可视化部分（如 Azure 仪表板和日志分析工作区摘要页）启动的编程查询或查询。 |

- 优化查询，如 Azure[监视器 中的"优化日志查询"中](../articles/azure-monitor/log-query/query-optimization.md)所述。
- 仪表板和工作簿可以在单个视图中包含多个查询，这些查询每次加载或刷新时都会生成突发的查询。 请考虑将它们分解为可按需加载的多个视图。 
- 在 Power BI 中，请考虑仅提取聚合结果，而不是原始日志。