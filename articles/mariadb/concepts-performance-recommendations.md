---
title: 性能建议-Azure Database for MariaDB
description: 本文介绍中的性能推荐功能 Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 05bc0f1ae50f74cc7c8ab2b236d73bdb4a6fe787
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84484716"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的性能建议

**适用于：** Azure Database for MariaDB 10。2

性能建议功能会分析数据库，为改进性能创建自定义建议。 为了生成建议，分析功能会查看各种数据库特征（包括架构）。 在服务器上启用[查询存储](concepts-query-store.md)，以充分利用性能建议功能。 如果性能架构已关闭，则启用查询存储会启用性能架构和此功能所需的一部分性能架构检测。 实施任何性能建议后，应测试性能以评估这些更改的影响。

## <a name="permissions"></a>权限

使用性能建议功能运行分析所需的“所有者”或“参与者”权限。

## <a name="performance-recommendations"></a>性能建议

[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

在 MariaDB 服务器的 "Azure 门户" 页上，从菜单栏的 "**智能性能**" 部分打开 "**性能建议**"。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="性能建议登陆页面":::

选择“分析”并选择一个数据库，该数据库将开始分析。 分析可能需要几分钟的时间才能完成，具体取决于你的工作负荷。 分析完成后，门户中将出现通知。 分析会对数据库进行深入检查。 建议你在非高峰期执行分析。

“建议”窗口会显示建议（如果找到任何建议）以及生成该建议的相关查询 ID 的列表。 使用查询 ID，可以通过 [mysql.query_store](concepts-query-store.md#mysqlquery_store) 视图来详细了解该查询。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="性能建议新页":::

系统不会自动应用建议。 若要应用建议，请复制查询文本并从所选的客户端中运行该查询。 记住进行测试和监视，以便评估建议。

## <a name="recommendation-types"></a>建议类型

### <a name="index-recommendations"></a>索引建议

“创建索引”建议会建议创建新索引，以加速工作负荷中最频繁运行或最耗时的查询。 此建议类型需要启用[查询存储](concepts-query-store.md)。 查询存储会收集查询信息，并提供详细的查询运行时和频率统计信息，供分析功能用于提出建议。

### <a name="query-recommendations"></a>查询建议

查询建议为工作负荷中的查询建议优化和重写。 通过识别 MariaDB 查询反模式并对其进行语法修复，可以改进耗时查询的性能。 此建议类型需要启用查询存储。 查询存储会收集查询信息，并提供详细的查询运行时和频率统计信息，供分析功能用于提出建议。
## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MariaDB 中的[监视和优化](concepts-monitoring.md)。