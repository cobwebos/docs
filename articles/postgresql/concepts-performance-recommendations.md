---
title: 性能建议-Azure Database for PostgreSQL-单服务器
description: 本文介绍 Azure Database for PostgreSQL-单服务器中的性能建议功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768463"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 中的性能建议-单服务器

**适用于：** Azure Database for PostgreSQL-单服务器版本9.6、10、11

性能建议功能分析数据库以创建自定义建议，以提高性能。 为了生成建议，分析功能会查看各种数据库特征（包括架构）。 在服务器上启用[查询存储](concepts-query-store.md)，以充分利用性能建议功能。 实施任何性能建议后，应测试性能以评估这些更改的影响。 

## <a name="permissions"></a>权限
使用性能建议功能运行分析所需的“所有者”或“参与者”权限。

## <a name="performance-recommendations"></a>性能建议
[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

在 PostgreSQL 服务器的 "Azure 门户" 页上，从菜单栏的 "**智能性能**" 部分打开 "**性能建议**"。

![性能建议登陆页面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

选择“分析”并选择一个数据库，该数据库将开始分析。 根据工作负荷的不同，第5个分析可能需要几分钟才能完成。 分析完成后，门户中将出现通知。 分析会对数据库进行深入检查。 建议你在非高峰期执行分析。 

"**建议**" 窗口将显示建议列表（如果已找到）。

![性能建议新页](./media/concepts-performance-recommendations/performance-recommendations-result.png)

系统不会自动应用建议。 若要应用建议，请复制查询文本并从所选的客户端中运行该查询。 记住进行测试和监视，以便评估建议。 

## <a name="recommendation-types"></a>建议类型

目前支持两种类型的建议： *Create index*和*Drop index*。

### <a name="create-index-recommendations"></a>创建索引建议
“创建索引”建议会建议创建新索引，以加速工作负荷中最频繁运行或最耗时的查询。 此建议类型需要启用[查询存储](concepts-query-store.md)。 查询存储会收集查询信息，并提供详细的查询运行时和频率统计信息，供分析功能用于提出建议。

### <a name="drop-index-recommendations"></a>删除索引建议
除了检测缺少的索引外，Azure Database for PostgreSQL 分析现有索引的性能。 如果索引很少使用或冗余，则分析器建议删除它。

## <a name="considerations"></a>注意事项
* 性能建议不适用于[读取副本](concepts-read-replicas.md)。
## <a name="next-steps"></a>后续步骤
- 了解有关如何在 Azure Database for PostgreSQL 中进行[监视和优化](concepts-monitoring.md)的详细信息。

