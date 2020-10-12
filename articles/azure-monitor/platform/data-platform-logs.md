---
title: Azure Monitor 中的日志 | Microsoft Docs
description: 介绍 Azure Monitor 中的日志，这些日志用于高级分析监视数据。
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032754"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor 日志概述
Azure Monitor 日志是 Azure Monitor 的一项功能，它收集和组织来自各种源的日志数据，并使其可用于使用复杂的查询语言进行分析。 不同源中的数据可以整合到单个工作区中，并进行分析以执行此类任务和趋势分析、警报和可视化。

## <a name="relationship-to-azure-monitor-metrics"></a>与 Azure Monitor 度量值的关系
Azure Monitor 度量值将数值数据存储在时间系列数据库中，这使得此数据比 Azure Monitor 日志更轻型，并支持近实时方案，使其特别适用于警报和快速检测问题。 不过，指标只能将数值数据存储在特定的结构中，而日志可以存储各种不同的数据类型，每个数据类型都有自己的结构。 你还可以使用不能用于分析指标数据的日志查询对日志数据执行复杂的分析。

除了指标之外，数值数据通常从数据源发送到日志。 虽然在日志中收集和保留此数据还有额外的费用，但它允许您在日志查询中包含指标数据，并使用其他监视数据对其进行分析。

## <a name="relationship-to-azure-data-explorer"></a>与 Azure 数据资源管理器的关系
Azure Monitor 日志基于 Azure 数据资源管理器。 Log Analytics 工作区大致等同于 Azure 数据资源管理器中的数据库，表的结构相同，并使用相同的 Kusto 查询语言 (KQL) 。 使用 Log Analytics 在 Azure 门户中处理 Azure Monitor 查询的体验与使用 Azure 数据资源管理器 Web UI 的经验类似。 甚至可以 [在 Azure 数据资源管理器查询中包含 Log Analytics 工作区中的数据](/azure/data-explorer/query-monitor-data)。 


## <a name="structure-of-data"></a>数据的结构
Azure Monitor 日志收集的数据存储在包含多个表的 [Log Analytics 工作区](./design-logs-deployment.md) 中，每个表存储特定源的数据。 工作区定义数据的地理位置、定义可访问数据的用户的访问权限，以及配置设置，如定价层和数据保留。 你可以对所有监视数据使用单个工作区，也可以根据你的要求创建多个工作区。 有关创建多个工作区的注意事项，请参阅 [设计 Azure Monitor 日志部署](design-logs-deployment.md) 。

每个工作区都包含多个表，这些表组织到具有多行数据的单独列中。 每个表由数据源提供的数据行共享的一组唯一列定义。 

[![Azure Monitor 日志结构](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Application Insights 中的日志数据也存储在 Azure Monitor 日志中，但根据应用程序的配置方式，存储的数据将有所不同。 对于基于工作区的应用程序，数据存储在一组标准表中的 Log Analytics 工作区中，用于保存应用程序请求、异常和页面视图等数据。 多个应用程序可以使用同一个工作区。 对于经典应用程序，数据不会存储在 Log Analytics 工作区中。 它使用相同的查询语言，并且你使用 Azure 门户中的相同 Log Analytics 工具创建并运行查询。 不过，经典应用程序的数据彼此分开存储。 尽管表名称和列名称不同，但其常规结构与基于工作区的应用程序相同。 有关这两种情况的详细比较，请参阅 [基于工作区的资源更改](../app/apm-tables.md) 。


> [!NOTE]
> 我们仍然针对 Application Insights 体验中的 Application Insights 经典资源查询、工作簿和基于日志的警报提供完全的后向兼容性。 若要根据[新的基于工作区的表结构/架构](../app/apm-tables.md)进行查询/查看，必须先导航到 Log Analytics 工作区。 在预览版期间，从 Application Insights 窗格中选择“日志”即可访问经典 Application Insights 查询体验。 有关更多详细信息，请参阅 [查询范围](../log-query/scope.md) 。


[![Azure Monitor 的日志结构 Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>日志查询
使用作为处理数据和返回结果的只读请求的 [日志查询](../log-query/log-query-overview.md) 从 Log Analytics 工作区中检索数据。 日志查询以 [Kusto 查询语言编写 (KQL) ](/azure/data-explorer/kusto/query/)，这是 Azure 数据资源管理器使用的查询语言。 使用 Log Analytics，它是 Azure 门户中的一种工具，用于编辑和运行日志查询并以交互方式分析其结果。 然后，可以使用所创建的查询来支持 Azure Monitor 如日志查询警报和工作簿中的其他功能。


## <a name="sources-of-data-for-azure-monitor-logs"></a>Azure Monitor 日志的数据源
Azure Monitor 从各种源收集日志数据，包括在 Azure Monitor 和虚拟机中运行的代理中的资源。 有关将数据发送到 Log Analytics 工作区的数据源的完整列表，请参阅 [什么是受 Azure Monitor 监视的内容](../monitor-reference.md) 。



## <a name="next-steps"></a>后续步骤

- 了解用于从 Log Analytics 工作区检索和分析数据的 [日志查询](../log-query/log-query-overview.md) 。
- 了解 [Azure Monitor 中的指标](data-platform-metrics.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。

