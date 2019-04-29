---
title: Azure Database for PostgreSQL 中查询存储功能的使用方案
description: 本文介绍 Azure Database for PostgreSQL 中“查询存储”功能的一些使用方案。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 873462354b70d13e56ca108c3257031ef34873f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563178"
---
# <a name="usage-scenarios-for-query-store"></a>查询存储的使用方案

**适用于：** Azure Database for PostgreSQL 9.6 和 10

可以在各种场景中使用查询存储，在这些场景中，跟踪和维护可预测的工作负载性能至关重要。 请考虑以下示例： 
- 识别和优化消耗最高的查询 
- A/B 测试 
- 在升级期间保持性能稳定 
- 识别并改进临时工作负载 

## <a name="identify-and-tune-expensive-queries"></a>识别并优化消耗高的查询 

### <a name="identify-longest-running-queries"></a>识别运行时间最长的查询 
使用 Azure 门户中的 [Query Performance Insight](concepts-query-performance-insight.md) 视图快速识别运行时间最长的查询。 这些查询往往占用大量资源。 优化运行时间最长的问题可以通过释放资源以供系统上运行的其他查询使用来提高性能。 

### <a name="target-queries-with-performance-deltas"></a>使用性能增量定位查询 
查询存储将性能数据切分为多个时间窗口，使你可以跟踪查询的性能。 这有助于精确地确定哪些查询会增加花费的总时间。 从而，你可以对工作负载进行有针对性的故障排除。

### <a name="tuning-expensive-queries"></a>优化消耗资源的查询 
发现查询的性能不佳时，采取的操作取决于问题的性质： 
- 使用[性能建议](concepts-performance-recommendations.md)确定是否有任何建议的索引。 如果有，请创建索引，然后使用“查询存储”评估创建索引后的查询性能。 
- 确保查询使用的基础表的统计信息是最新的。
- 考虑重新编写消耗大量资源的查询。 例如，利用查询参数化并减少动态 SQL 的使用。 在读取数据时实现最佳逻辑，例如在数据库端（而非应用程序端）应用数据过滤。 


## <a name="ab-testing"></a>A/B 测试 
使用查询存储功能比较工作负载在你计划引入的应用程序更改发生更改前后的性能。 使用查询存储评估环境或应用程序更改对工作负载性能的影响的方案示例： 
- 推出新版本的应用程序。 
- 向服务器添加更多资源。 
- 在消耗大量资源的查询引用的表上创建缺失的索引。 
 
在任何一个这样的方案中，应用以下工作流： 
1. 在计划的更改之前使用查询存储运行工作负载，以生成性能基线。 
2. 在受控时刻应用应用程序更改。 
3. 继续运行工作负载足够长的时间以在更改后生成系统的性能映像。 
4. 比较更改前后的结果。 
5. 决定是保留更改还是回退。 


## <a name="identify-and-improve-ad-hoc-workloads"></a>识别并改进临时工作负载 
某些工作负载没有可通过优化来提高应用程序整体性能的主查询。 它们通常具有相对大量的唯一查询，其中每一个都占用部分系统资源。 不经常执行每个唯一查询，因此就个别而言，它们的运行时消耗并不重要。 另一方面，假设应用程序一直在生成新查询，则系统资源的很大一部分会花费在查询编译上，这不是最佳选择。 通常情况下，如果你的应用程序生成查询（而不是使用存储过程或参数化查询），或者它依赖于默认生成查询的对象关系映射框架，则会发生这种情况。 
 
如果你能控制应用程序代码，则可以考虑重写数据访问层以使用存储过程或参数化查询。 但是，通过强制对整个数据库（所有查询）或具有相同查询哈希的单个查询模板进行查询参数化，可以在不更改应用程序的情况下改善这种情况。 

## <a name="next-steps"></a>后续步骤
- 了解有关[使用查询存储的最佳做法](concepts-query-store-best-practices.md)的详细信息