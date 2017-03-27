---
title: "监视 Azure 搜索服务中的使用情况和统计信息 | Microsoft Docs"
description: "跟踪 Azure 搜索（在 Microsoft Azure 上托管的云搜索服务）的资源消耗和索引大小。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e40acb01d6297aa66090859533fd48c108a9a88


---
# <a name="monitor-usage-and-query-metrics-in-an-azure-search-service"></a>监视 Azure 搜索服务中的使用情况和查询指标
Azure 搜索收集有关在服务级别所执行查询的统计信息，包括每秒查询次数 (QPS)、延迟以及在数量超过容量时所丢弃查询的百分比。 可在门户中通过“监视”边栏选项卡查看指标。

   ![QPS 活动屏幕截图][5]

本文介绍服务总体的指标。 为了深入了解索引级别的活动，请启用搜索流量分析并使用 Power BI 查看分析。 从访问 [Azure 搜索的搜索流量分析](search-traffic-analytics.md)开始操作。

## <a name="view-query-throughput-statistics"></a>查看查询吞吐量统计信息
单击服务仪表板上的“监视”磁贴打开“监视”边栏选项卡。

   ![“监视”磁贴][2]

服务级别的查询活动分别针对查询执行、延迟和限制进行记录。 指标持续收集，但门户可能需要几分钟时间才能显示最新活动。 

单击“指标”磁贴查看详细信息、添加警报、启用诊断或编辑图表。

  ![“指标”边栏选项卡上的命令][4]

### <a name="set-up-alerts"></a>设置警报
可以在指标详细信息页上配置警报，以便在查询执行、延迟或限制活动超出为其定义的标准时触发电子邮件通知。

### <a name="enable-diagnostics"></a>启用诊断
打开诊断时，可以配置诊断数据的存储位置、是否包含操作日志和指标，以及数据的保留时间。

### <a name="change-chart-type-and-data-collection-interval"></a>更改图表类型和数据收集间隔
对于每个指标，可以单击“编辑”更改可视化效果（从折线图到条形图），或修改 x 轴以覆盖不同时间范围。

  ![时间范围配置][3]

## <a name="view-counts-and-resource-usage-in-the-portal"></a>在门户中查看计数和资源使用情况
跟踪索引和文档大小的增长有助于在达到已为服务建立的上限之前主动调整容量。 

若要监视资源使用情况，请在[门户](https://portal.azure.com)中查看服务的计数和统计信息。 如果要构建自定义服务管理工具，还可以采用编程方式获取信息。

1. 登录[门户](https://portal.azure.com)。 
2. 打开 Azure 搜索服务的服务仪表板。 可以在主页上找到服务磁贴，或者可以从跳转栏上的“浏览”来浏览到服务。 

“使用情况”部分包含的指标告知你当前正在使用可用资源的哪些部分。 有关索引、文档和存储的每个服务限制的信息，请参阅[服务限制](search-limits-quotas-capacity.md)。

  ![“使用情况”磁贴][1]

> [!NOTE]
> 上面的屏幕截图对应免费服务，每个服务最多具有一个副本和一个分区，并且只能承载 3 个索引、10000 个文档或 50 MB 数据（以先发生的为准）。 在“基本”或“标准”层上创建的服务具有更大的服务限制。 有关如何选择层的更多信息，请参阅[选择层或 SKU](search-sku-tier.md)。
> 
> 

### <a name="get-index-statistics-using-the-rest-api"></a>使用 REST API 获取索引统计信息
Azure 搜索 REST API 和 .NET SDK 支持采用编程方式访问服务指标。  如果要使用[索引器](https://msdn.microsoft.com/library/azure/dn946891.aspx)从 Azure SQL 数据库或 DocumentDB 加载索引，可使用一个额外的 API 获取所需数字。 

* [获取索引统计信息](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [计数文档](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [获取索引器状态](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>后续步骤
请查看[扩展副本和分区](search-limits-quotas-capacity.md)获取有关以下内容的指南：如何平衡为现有服务分配的分区和副本。 

请访问[管理 Microsoft Azure 上的搜索服务](search-manage.md)，获取有关服务管理的更多信息，或访问[性能和优化](search-performance-optimization.md)获取优化指南。

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[2]: ./media/search-monitor-usage/AzSearch-Monitor-Tile.PNG
[3]: ./media/search-monitor-usage/AzSearch-Monitor-Intervals.PNG
[4]: ./media/search-monitor-usage/AzSearch-Monitor-AlertCmd.PNG
[5]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG








<!--HONumber=Nov16_HO3-->


