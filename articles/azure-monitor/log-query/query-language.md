---
title: Azure Monitor 日志查询 | Microsoft Docs
description: 有关如何在 Azure Monitor 中编写日志查询的资源参考。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 7c6530416f0eb6b822b6f5f3eb53f59aeae05d53
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894349"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor 日志查询
Azure Monitor 日志在 Azure 数据资源管理器之上构建，Azure Monitor 日志查询使用同一 Kusto 查询语言的某个版本。 [Kusto 查询语言文档](/azure/kusto/query)提供了该语言的完整详细信息，在编写 Azure Monitor 日志查询时，应将此文档用作主要参考资源。 本页提供了用于学习编写查询，以及该语言的 Azure Monitor 实现差异的其他资源的链接。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>入门

- [开始使用 Azure Monitor Log Analytics](get-started-portal.md)是编写查询并在 Azure 门户中处理结果的课程。
- [Azure Monitor 日志查询入门](get-started-queries.md)是一门介绍如何使用 Azure Monitor 日志数据编写查询的课程。

## <a name="concepts"></a>概念
- [在 Azure Monitor 中分析日志数据](../../azure-monitor/log-query/log-query-overview.md)简要概述了日志查询以及如何构建 Azure Monitor 日志数据。
- [查看和分析 Azure Monitor 中的日志数据](../../azure-monitor/log-query/portals.md)介绍了可在其中创建和运行日志查询的门户。

## <a name="reference"></a>参考

- [查询语言参考](/azure/kusto/query)是 Kusto 查询语言的完整语言参考。
- [Azure Monitor 日志查询语言差异](data-explorer-difference.md)介绍了不同 Kusto 查询语言版本之间的差异。
- [Azure Monitor 日志记录中的标准属性](../../azure-monitor/platform/log-standard-properties.md)介绍了所有 Azure Monitor 日志数据的标准属性。
- [在 Azure Monitor 中执行跨资源日志查询](../../azure-monitor/log-query/cross-workspace-query.md)介绍了如何编写使用多个 Log Analytics 工作区和 Application Insights 应用程序中的数据的日志查询。


## <a name="examples"></a>示例

- [Azure Monitor 日志查询示例](examples.md)提供了使用 Azure Monitor 日志数据的示例查询。



## <a name="lessons"></a>课程

- [在 Azure Monitor 日志查询中使用字符串](string-operations.md)介绍了如何使用字符串数据。
- [在 Azure Monitor 日志查询中使用日期时间值](datetime-operations.md)介绍了如何使用日期和时间数据。 
- [Azure Monitor 日志查询中的聚合](aggregations.md)和 [Azure Monitor 日志查询中的高级聚合](advanced-aggregations.md)介绍了如何聚合和汇总数据。
- [Azure Monitor 日志查询中的联接](joins.md)介绍了如何联接多个表中的数据。
- [在 Azure Monitor 日志查询中使用 JSON 和数据结构](json-data-structures.md)介绍了如何分析 JSON 数据。
- [在 Azure Monitor 中编写高级日志查询](advanced-query-writing.md)介绍了创建复杂查询和重用代码的策略。
- [通过 Azure Monitor 日志查询创建图表和关系图](charts.md)介绍了如何将日志查询中的数据可视化。

## <a name="cheatsheets"></a>速查表

-  [从 SQL 到 Azure Monitor 日志查询](sql-cheatsheet.md)可为已经熟悉 SQL 的用户提供帮助。
-  [从 Splunk 到 Azure Monitor 日志查询](splunk-cheatsheet.md)可为已经熟悉 Splunk 的用户提供帮助。
 
## <a name="next-steps"></a>后续步骤

- 访问完整的 [Kusto 查询语言的参考文档](/azure/kusto/query/)。
