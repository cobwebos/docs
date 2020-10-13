---
title: 性能建议-Azure Database for PostgreSQL-单服务器
description: 本文介绍 Azure Database for PostgreSQL-单服务器中的性能建议功能。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: d547844671e6485e71be7dd8c355de08f3dec5e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710560"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 中的性能建议-单服务器

**适用于：** Azure Database for PostgreSQL-单服务器版本9.6、10、11

性能建议功能分析数据库以创建自定义建议，以提高性能。 为了生成建议，分析将查看各种数据库特征（包括架构）。 启用服务器上的[查询存储](concepts-query-store.md)即可充分利用性能建议功能。 实施任何性能建议后，应测试性能以评估这些更改的影响。 

## <a name="permissions"></a>权限
使用性能建议功能运行分析所需的“所有者”或“参与者”权限。

## <a name="performance-recommendations"></a>性能建议
[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

在 PostgreSQL 服务器的 "Azure 门户" 页上，从菜单栏的 "**智能性能**" 部分打开 "**性能建议**"。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="性能建议登陆页面&quot;:::

选择“分析”并选择数据库就会开始分析。 根据工作负荷的不同，第5个分析可能需要几分钟才能完成。 分析完成后，门户中将出现通知。 分析会执行数据库的深层检查。 建议在非高峰期执行分析。 

&quot; **建议** " 窗口将显示建议列表（如果已找到）。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="性能建议登陆页面&quot;:::

选择“分析”并选择数据库就会开始分析。 根据工作负荷的不同，第5个分析可能需要几分钟才能完成。 分析完成后，门户中将出现通知。 分析会执行数据库的深层检查。 建议在非高峰期执行分析。 

&quot; **建议** ":::

不会自动应用建议。 若要应用建议，请复制查询文本并从所选的客户端中运行。 记住通过测试和监视来评估建议。 

## <a name="recommendation-types"></a>建议类型

目前支持两种类型的建议： *Create index* 和 *Drop index*。

### <a name="create-index-recommendations"></a>创建索引建议
“创建索引”建议建议使用新索引来加快工作负载中最常运行或最耗时的查询。 此建议类型需要启用[查询存储](concepts-query-store.md)。 查询存储收集查询信息并提供详细的查询运行时和频率统计信息，供分析用来提出建议。

### <a name="drop-index-recommendations"></a>删除索引建议
除了检测缺少的索引外，Azure Database for PostgreSQL 分析现有索引的性能。 如果索引很少使用或冗余，则分析器建议删除它。

## <a name="considerations"></a>注意事项
* 性能建议不适用于 [读取副本](concepts-read-replicas.md)。
## <a name="next-steps"></a>后续步骤
- 了解有关如何在 Azure Database for PostgreSQL 中进行[监视和优化](concepts-monitoring.md)的详细信息。

