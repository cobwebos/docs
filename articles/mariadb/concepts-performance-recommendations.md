---
title: Azure 数据库中的 MariaDB 的性能建议
description: 本文介绍 Azure 数据库中的性能建议功能 for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a2f9b7597022822272692d20976e1da654b9d524
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462053"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Azure 数据库中的 MariaDB 的性能建议

**适用于：** Azure Database for MariaDB 10.2

> [!NOTE]
> 性能建议处于预览状态。

性能建议功能分析数据库以创建自定义的建议以提高性能。 若要生成的建议，分析探讨各种数据库特征包括架构。 启用[Query Store](concepts-query-store.md)以充分利用性能建议功能在服务器上。 如果性能架构为 OFF，启用查询存储启用 performance_schema 和功能所需的性能架构 instruments 的子集。 实现任何性能建议之后, 您应该测试性能以评估这些更改的影响。

## <a name="permissions"></a>权限

使用性能建议功能运行分析所需的  “所有者”或“参与者”  权限。

## <a name="performance-recommendations"></a>性能建议

[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

打开**性能建议**从**智能性能**MariaDB 服务器在 Azure 门户页上的菜单栏区域。

![性能建议登陆页面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

选择**分析**，然后选择一个数据库，它将开始分析。 根据工作负荷分析可能需要几分钟才能完成。 分析完成后，门户中将出现通知。 分析执行深入检查您的数据库。 我们建议在非高峰期执行分析。

**建议**窗口将显示建议是否发现了任何生成此建议的相关的查询 ID 的列表。 查询 ID，您可以使用[mysql.query_store](concepts-query-store.md#mysqlquery_store)视图若要了解有关查询的详细信息。

![性能建议新页](./media/concepts-performance-recommendations/performance-recommendations-result.png)

不会自动应用建议。 若要应用建议，请复制查询文本，并从所选的客户端运行。 请记住要测试和监视来评估该建议。

## <a name="recommendation-types"></a>建议类型

目前，仅*Create Index*支持的建议。

### <a name="create-index-recommendations"></a>创建索引建议

*创建索引*建议会推荐新的索引来提高工作负荷中的最大频率耗时或运行查询速度。 此建议类型需要[Query Store](concepts-query-store.md)才可用。 查询存储收集查询的信息，并提供详细的查询运行时和频率的统计信息分析用来使该建议。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[监视和优化](concepts-monitoring.md)Azure Database for MariaDB 中。