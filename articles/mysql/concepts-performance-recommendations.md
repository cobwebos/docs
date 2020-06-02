---
title: 性能建议 - Azure Database for MySQL
description: 本文介绍 Azure Database for MySQL 中的性能建议功能
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: cba07b84a1ddc2b9362c818ae2d3747b98379f2e
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402643"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的性能建议

**适用于：** Azure Database for MySQL 5.7、8.0

性能建议功能会分析数据库，为改进性能创建自定义建议。 为了生成建议，分析功能会查看各种数据库特征（包括架构）。 在服务器上启用[查询存储](concepts-query-store.md)，以充分利用性能建议功能。 如果性能架构已关闭，则启用查询存储会启用性能架构和此功能所需的一部分性能架构检测。 实施任何性能建议后，应测试性能以评估这些更改的影响。

## <a name="permissions"></a>权限

使用性能建议功能运行分析所需的“所有者”或“参与者”权限。

## <a name="performance-recommendations"></a>性能建议

[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

从 MySQL 服务器的 Azure 门户页上的菜单栏的“智能性能”部分中打开“性能建议”。

![性能建议登陆页面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

选择“分析”并选择一个数据库，该数据库将开始分析。 分析可能需要几分钟的时间才能完成，具体取决于你的工作负荷。 分析完成后，门户中将出现通知。 分析会对数据库进行深入检查。 建议你在非高峰期执行分析。

“建议”窗口会显示建议（如果找到任何建议）以及生成该建议的相关查询 ID 的列表。 使用查询 ID，可以通过 [mysql.query_store](concepts-query-store.md#mysqlquery_store) 视图来详细了解该查询。

![性能建议新页](./media/concepts-performance-recommendations/performance-recommendations-result.png)

系统不会自动应用建议。 若要应用建议，请复制查询文本并从所选的客户端中运行该查询。 记住进行测试和监视，以便评估建议。

## <a name="recommendation-types"></a>建议类型

目前仅支持“创建索引”建议。

### <a name="create-index-recommendations"></a>创建索引建议

“创建索引”建议会建议创建新索引，以加速工作负荷中最频繁运行或最耗时的查询。 此建议类型需要启用[查询存储](concepts-query-store.md)。 查询存储会收集查询信息，并提供详细的查询运行时和频率统计信息，供分析功能用于提出建议。

## <a name="next-steps"></a>后续步骤
- 详细了解如何在 Azure Database for MySQL 中进行[监视和优化](concepts-monitoring.md)。