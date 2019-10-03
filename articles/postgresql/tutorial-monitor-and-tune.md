---
title: 有关在 Azure Database for PostgreSQL（单一服务器）中进行监视和优化的教程
description: 本教程逐步介绍如何在 Azure Database for PostgreSQL（单一服务器）中进行监视和优化。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: dcd8363db33809e2a33885f43e570c53807505fd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073091"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>教程：监视和优化 Azure Database for PostgreSQL（单一服务器）

Azure Database for PostgreSQL 提供有助于了解和改进服务器性能的功能。 在本教程中，将了解如何：
> [!div class="checklist"]
> * 启用查询和等待统计信息收集
> * 访问和利用收集的数据
> * 查看查询性能和等待一段时间内的统计信息
> * 分析数据库以获取性能建议
> * 应用性能建议

## <a name="before-you-begin"></a>开始之前
需要一个使用 PostgreSQL 版本 9.6 或 10 的 Azure Database for PostgreSQL 服务器。 可以按照[创建教程](tutorial-design-database-using-azure-portal.md)中的步骤创建服务器。

> [!IMPORTANT]
> 查询存储  、Query Performance Insight  和性能建议  均在公共预览版中提供。

## <a name="enabling-data-collection"></a>允许收集数据
[查询存储](concepts-query-store.md)捕获服务器上的查询和等待统计信息的历史记录，并将其存储在服务器上的 azure_sys  数据库中。 这是一个可选的功能。 若要启用此功能，请执行以下操作：

1. 打开 Azure 门户。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在左侧菜单的“设置”部分中选择“服务器参数”   。

4. 将 pg_qs.query_capture_mode  设置为“TOP”  以开始收集查询性能数据。 将 pgms_wait_sampling.query_capture_mode  设置为“ALL”  以开始收集等待统计信息。 保存。
   
   ![查询存储服务器参数](./media/tutorial-performance-intelligence/query-store-parameters.png)

5. 允许第一批数据在 azure_sys  数据库中最多保留 20 分钟。


## <a name="performance-insights"></a>性能见解
Azure 门户中的 [Query Performance Insight](concepts-query-performance-insight.md) 视图将显示来自查询存储的关键信息的可视化效果。 

1. 在 Azure Database for PostgreSQL 服务器的门户页面中，选择左侧菜单的“支持 + 疑难解答”  部分下的“Query Performance Insight”  。

2. “长时间运行查询”  选项卡按平均每次执行持续时间显示前 5 个查询，每隔 15 分钟聚合一次。 
   
   ![Query Performance Insight 登陆页面](./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png)

   可以通过从“查询数量”  下拉列表中进行选择来查看更多查询。 执行此操作时，特定查询 ID 的图表颜色可能会更改。

3. 可以在图表中单击并拖动以缩小到特定的时间窗口。

4. 使用放大和缩小图标分别查看更短或更长的时间段。

5. 查看图表下方的表，以了解有关该时间窗口中长时间运行的查询的更多详细信息。

6. 选择“等待统计信息”  选项卡以查看服务器中等待次数的相应可视化效果。
   
   ![Query Performance Insight 等待统计信息](./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png)

### <a name="permissions"></a>权限
查看 Query Performance Insight 中查询文本所需的“所有者”  或“参与者”  权限。 读者  可以查看图表和表格，但不能查看查询文本。


## <a name="performance-recommendations"></a>性能建议
[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

1. 从 PostgreSQL 服务器的 Azure 门户页上的菜单栏的“支持 + 疑难解答”  部分中打开“性能建议”  。
   
   ![性能建议登陆页面](./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png)

2. 选择“分析”  并选择数据库。 随即会开始分析。

3. 这可能需要几分钟的时间才能完成，具体取决于你的工作负载。 分析完成后，门户中将出现通知。

4. 如果无发现，则  “性能建议”窗口将显示建议列表。 

5. 建议将显示有关相关“数据库”  、“表”  、“列”  和“索引大小”  的信息。

   ![“性能建议”结果](./media/tutorial-performance-intelligence/performance-recommendations-result.png)

6. 若要实施建议，请复制查询文本并从所选的客户端中运行。

### <a name="permissions"></a>权限
使用性能建议功能运行分析所需的  “所有者”或“参与者”  权限。

## <a name="next-steps"></a>后续步骤
- 了解有关如何在 Azure Database for PostgreSQL 中进行[监视和优化](concepts-monitoring.md)的详细信息。