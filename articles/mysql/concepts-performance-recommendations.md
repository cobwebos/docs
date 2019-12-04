---
title: 性能建议-Azure Database for MySQL
description: 本文介绍中的性能推荐功能 Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f957459cf20a018ae53ba6ec90fb478dd0c69044
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770894"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的性能建议

**适用于：** Azure Database for MySQL 5。7

性能建议功能会分析数据库，以创建自定义的建议，以提高性能。 若要生成建议，分析将查看各种数据库特征，包括架构。 启用服务器上的[查询存储](concepts-query-store.md)，以充分利用性能建议功能。 如果性能架构处于关闭状态，则启用查询存储会启用此功能所需的 performance_schema 和性能架构检测子集。 实现任何性能建议后，应测试性能以评估这些更改的影响。

## <a name="permissions"></a>权限

使用性能建议功能运行分析所需的“所有者”或“参与者”权限。

## <a name="performance-recommendations"></a>性能建议

[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

从 MySQL 服务器的 "Azure 门户" 页上菜单栏的 "**智能性能**" 部分中打开 "**性能建议**"。

![性能建议登陆页面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

选择 "**分析**"，然后选择将开始分析的数据库。 根据工作负荷，分析可能需要几分钟才能完成。 分析完成后，门户中将出现通知。 分析执行数据库的深层检查。 建议在非高峰期执行分析。

"**建议**" 窗口将显示建议列表（如果找到）以及生成此建议的相关查询 ID。 使用查询 ID，你可以使用[query_store](concepts-query-store.md#mysqlquery_store)视图来了解有关查询的详细信息。

![性能建议新页](./media/concepts-performance-recommendations/performance-recommendations-result.png)

不会自动应用建议。 若要应用建议，请复制查询文本，并从所选客户端运行该查询。 请记住测试和监视以评估建议。

## <a name="recommendation-types"></a>建议类型

目前仅支持*创建索引*建议。

### <a name="create-index-recommendations"></a>创建索引建议

*Create Index*建议建议使用新索引来加快工作负荷中最常运行或耗时的查询。 此建议类型需要启用[查询存储](concepts-query-store.md)。 查询存储收集查询信息并提供分析用于提出建议的详细查询运行时和频率统计信息。

## <a name="next-steps"></a>后续步骤
- 详细了解 Azure Database for MySQL 中的[监视和优化](concepts-monitoring.md)。