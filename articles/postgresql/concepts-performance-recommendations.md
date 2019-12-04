---
title: 性能建议-Azure Database for PostgreSQL-单服务器
description: 本文介绍 Azure Database for PostgreSQL-单服务器中的性能建议功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768463"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 中的性能建议-单服务器

**适用于：** Azure Database for PostgreSQL-单服务器版本9.6、10、11

性能建议功能分析数据库以创建自定义建议，以提高性能。 若要生成建议，分析将查看各种数据库特征，包括架构。 启用服务器上的[查询存储](concepts-query-store.md)，以充分利用性能建议功能。 实现任何性能建议后，应测试性能以评估这些更改的影响。 

## <a name="permissions"></a>权限
使用性能建议功能运行分析所需的“所有者”或“参与者”权限。

## <a name="performance-recommendations"></a>性能建议
[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

在 PostgreSQL 服务器的 "Azure 门户" 页上，从菜单栏的 "**智能性能**" 部分打开 "**性能建议**"。

![性能建议登陆页面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

选择 "**分析**"，然后选择将开始分析的数据库。 根据工作负荷的不同，第5个分析可能需要几分钟才能完成。 分析完成后，门户中将出现通知。 分析执行数据库的深层检查。 建议在非高峰期执行分析。 

"**建议**" 窗口将显示建议列表（如果已找到）。

![性能建议新页](./media/concepts-performance-recommendations/performance-recommendations-result.png)

不会自动应用建议。 若要应用建议，请复制查询文本，并从所选客户端运行该查询。 请记住测试和监视以评估建议。 

## <a name="recommendation-types"></a>建议类型

目前支持两种类型的建议： *Create index*和*Drop index*。

### <a name="create-index-recommendations"></a>创建索引建议
*Create Index*建议建议使用新索引来加快工作负荷中最常运行或耗时的查询。 此建议类型需要启用[查询存储](concepts-query-store.md)。 查询存储收集查询信息并提供分析用于提出建议的详细查询运行时和频率统计信息。

### <a name="drop-index-recommendations"></a>删除索引建议
除了检测缺少的索引外，Azure Database for PostgreSQL 分析现有索引的性能。 如果索引很少使用或冗余，则分析器建议删除它。

## <a name="considerations"></a>注意事项
* 性能建议不适用于[读取副本](concepts-read-replicas.md)。
## <a name="next-steps"></a>后续步骤
- 了解有关如何在 Azure Database for PostgreSQL 中进行[监视和优化](concepts-monitoring.md)的详细信息。

