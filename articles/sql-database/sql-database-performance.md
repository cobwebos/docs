---
title: 监视和提高性能 - Azure SQL 数据库 | Microsoft Docs
description: Azure SQL 数据库提供的性能工具有助于发现可以提高当前查询性能的方面。
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 8a458af27bd517be7c3ce0b5ad30c6d575d494c0
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522435"
---
# <a name="monitor-and-improve-performance"></a>监视和提高性能
Azure SQL 数据库确定数据库中的潜在问题，并通过提供智能优化操作和建议来建议可以提高工作负荷性能的操作。

若要查看数据库性能，请使用“概述”页上的“性能”磁贴，或向下导航至“支持 + 故障排除”部分：

   ![查看性能](./media/sql-database-performance/entries.png)

在“支持 + 故障排除”部分中，可以使用以下页面：


1. [性能概述](#performance-overview)，用于监视数据库的性能。 
2. [性能建议](#performance-recommendations)，用于查找可以改进工作负荷性能的性能建议。
3. [查询性能见解](#query-performance-insight)，用于查找资源消耗排名靠前的查询。
4. [自动优化](#automatic-tuning)，可让 Azure SQL 数据库自动优化数据库。

## <a name="performance-overview"></a>性能概述
此视图提供了数据库性能摘要，有助于进行性能优化和故障排除。 

![性能](./media/sql-database-performance/performance.png)

* “建议”磁贴提供数据库的优化建议明细（如果建议超出 3 条，则将显示前 3 条）。 单击此磁贴可转到**[性能建议](#performance-recommendations)**。 
* “**优化活动**”磁贴提供正在进行的和已完成的数据库优化操作摘要，可快速查看优化活动的历史记录。 单击此磁贴可转到数据库的完整优化历史记录视图。
* “自动优化”磁贴显示数据库的[自动优化配置](sql-database-automatic-tuning-enable.md)（自动应用于数据库的优化选项）。 单击此磁贴可打开自动化配置对话框。
* “**数据库查询**”磁贴显示数据库的查询性能摘要（DTU 整体使用情况和排名靠前的资源消耗查询）。 单击此磁贴可转到**[查询性能见解](#query-performance-insight)**。

## <a name="performance-recommendations"></a>性能建议
此页提供可提高数据库性能的智能[优化建议](sql-database-advisor.md)。 此页上显示了以下类型的建议：

* 要在其上创建或删除索引的建议。
* 数据库中发现架构问题时提供的建议。
* 查询可从参数化查询中获益时提供的建议。

![性能](./media/sql-database-performance/recommendations.png)

还可找到过去应用的优化操作的完整历史记录。

在[查找并应用性能建议](sql-database-advisor-portal.md)一文中了解如何查找应用性能建议。

## <a name="automatic-tuning"></a>自动优化
Azure SQL 数据库可以通过应用[性能建议](sql-database-advisor.md)自动优化数据库性能。 若要了解详细信息，请阅读[自动优化文章](sql-database-automatic-tuning.md)。 若要启用它，请阅读[如何启用自动优化](sql-database-automatic-tuning-enable.md)。

## <a name="query-performance-insight"></a>查询性能见解
[查询性能见解](sql-database-query-performance.md) 通过提供以下功能使排查数据库性能问题花费的时间更少：

* 深入洞察数据库资源 (DTU) 的消耗。 
* 排名靠前的 CPU 消耗查询，经过优化后可能会改善性能。 
* 可以向下钻取查询的详细信息。 

  ![性能仪表板](./media/sql-database-query-performance/performance.png)

在**[如何使用 Query Performance Insight](sql-database-query-performance.md)** 一文中找到有关此页的详细信息。

## <a name="additional-resources"></a>其他资源
* [Azure SQL 数据库的单一数据库性能指导](sql-database-performance-guidance.md)
* [何时应使用弹性池？](sql-database-elastic-pool-guidance.md)

