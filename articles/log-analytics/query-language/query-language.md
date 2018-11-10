---
title: Azure Monitor Log Analytics 查询语言 | Microsoft Docs
description: 有关如何在 Log Analytics 中编写查询的资源参考。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f8bef8c6ab5c0639f9a99eb2c0443c33d7b6d84e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243812"
---
# <a name="log-analytics-query-language"></a>Log Analytics 查询语言
Log Analytics 为 Azure Monitor 提供日志收集和分析。 它构建在 Azure 数据资源管理器的基础之上，并使用相同查询语言的某个版本。 [Azure 数据资源管理器查询语言文档](/azure/kusto/query)提供了该语言的完整详细信息，在编写 Log Analytics 查询时，应将此文档用作主要参考资源。 本页提供了用于学习编写查询，以及该语言的 Log Analytics 实现差异的其他资源的链接。

## <a name="getting-started"></a>入门

- [在 Azure 门户中开始使用 Log Analytics](get-started-analytics-portal.md) 课程介绍了如何在 Azure 门户中编写查询和处理结果。
-  [Log Analytics 中的查询入门](get-started-queries.md)课程介绍了如何使用 Log Analytics 数据编写查询。

## <a name="concepts"></a>概念
- [在 Azure Monitor 中分析 Log Analytics 数据](../log-analytics-queries.md)简要概述了日志查询以及如何构建 Log Analytics 数据。
- [查看和分析 Log Analytics 中的数据](../log-analytics-log-search-portals.md)介绍了可在其中创建和运行 Log Analytics 查询的门户。

## <a name="reference"></a>引用

- [查询语言参考](/azure/kusto/query)是数据资源管理器查询语言的完整语言参考。
- [Log Analytics 查询语言差异](data-explorer-difference.md)介绍了不同数据资源管理器查询语言版本之间的差异。
- [Log Analytics 记录中的标准属性](../log-analytics-standard-properties.md)介绍了所有 Log Analytics 数据的标准属性。
- [在 Log Analytics 中执行跨资源日志搜索](../log-analytics-cross-workspace-search.md)介绍了如何编写使用多个 Log Analytics 工作区和 Application Insights 应用程序中的数据的查询。


## <a name="examples"></a>示例

- [Log Analytics 查询示例](examples.md)提供了使用 Log Analytics 数据的示例查询。



## <a name="lessons"></a>课程

- [在 Log Analytics 查询中使用字符串](string-operations.md)介绍了如何使用字符串数据。
- [在 Log Analytics 查询中使用日期时间值](datetime-operations.md)介绍了如何使用日期和时间数据。 
- [Log Analytics 查询中的聚合](aggregations.md)和 [Log Analytics 查询中的高级聚合](advanced-aggregations.md)介绍了如何聚合和汇总数据。
- [Log Analytics 查询中的联接](joins.md)介绍了如何联接多个表中的数据。
- [在 Log Analytics 查询中使用 JSON 和数据结构](json-data-structures.md)介绍了如何分析 JSON 数据。
- [在 Log Analytics 中编写高级查询](advanced-query-writing.md)介绍了创建复杂查询和重用代码的策略。
- [通过 Log Analytics 查询创建图表和关系图](charts.md)介绍了如何将查询中的数据可视化。

## <a name="cheatsheets"></a>速查表

-  [从 SQL 到 Log Analytics 查询语言备忘单](sql-cheatsheet.md)可为已经熟悉 SQL 的用户提供帮助。
-  [从 Splunk 到 Log Analytics 查询语言备忘单](sql-cheatsheet.md)可为已经熟悉 Splunk 的用户提供帮助。
 
## <a name="next-steps"></a>后续步骤

- 访问完整的[数据资源管理器查询语言参考文档](/azure/kusto/query/)。