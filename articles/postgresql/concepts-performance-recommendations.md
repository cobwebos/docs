---
title: 性能建议 - Azure 数据库，用于 PostgreSQL - 单个服务器
description: 本文介绍了 Azure 数据库中的性能建议功能，用于 PostgreSQL - 单服务器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768463"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Azure 数据库中针对 PostgreSQL - 单服务器的性能建议

**适用于：** 用于 PostgreSQL 的 Azure 数据库 - 单服务器版本 9.6、10、11

性能建议功能分析数据库以创建自定义建议以提高性能。 要生成建议，分析将查看各种数据库特征，包括架构。 使[服务器上的查询存储](concepts-query-store.md)充分利用性能建议功能。 实现任何性能建议后，应测试性能以评估这些更改的影响。 

## <a name="permissions"></a>权限
使用性能建议功能运行分析所需的****“所有者”或“参与者”**** 权限。

## <a name="performance-recommendations"></a>性能建议
[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

从 PostgreSQL 服务器 Azure 门户页上的菜单栏的**智能性能**部分打开**性能建议**。

![性能建议登陆页面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

选择 **"分析**并选择一个数据库"，该数据库将开始分析。 根据您的工作负载，分析可能需要几分钟才能完成。 分析完成后，门户中将出现通知。 分析对数据库进行深度检查。 我们建议您在非高峰时段执行分析。 

如果找到任何建议，"**建议"** 窗口将显示建议列表。

![性能建议新页](./media/concepts-performance-recommendations/performance-recommendations-result.png)

建议不会自动应用。 要应用建议，请复制查询文本并从您选择的客户端运行它。 记得测试和监视以评估建议。 

## <a name="recommendation-types"></a>建议类型

目前，支持两种类型的建议：*创建索引*和*删除索引*。

### <a name="create-index-recommendations"></a>创建索引建议
*创建索引*建议建议新的索引，以加快工作负载中最常运行或耗时的查询。 此建议类型需要启用[查询存储](concepts-query-store.md)。 查询存储收集查询信息，并提供分析用于生成建议的详细查询运行时和频率统计信息。

### <a name="drop-index-recommendations"></a>删除索引建议
除了检测缺少的索引外，PostgreSQL 的 Azure 数据库还分析现有索引的性能。 如果索引很少使用或冗余，分析器建议将其删除。

## <a name="considerations"></a>注意事项
* 性能建议不适用于[读取副本](concepts-read-replicas.md)。
## <a name="next-steps"></a>后续步骤
- 了解有关如何在 Azure Database for PostgreSQL 中进行[监视和优化](concepts-monitoring.md)的详细信息。

