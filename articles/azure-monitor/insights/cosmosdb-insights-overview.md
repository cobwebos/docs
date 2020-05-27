---
title: 使用适用于 Cosmos DB 的 Azure Monitor 监视 Azure Cosmos DB | Microsoft Docs
description: 本文介绍了适用于 Cosmos DB 的 Azure Monitor 功能。使用此功能，Cosmos DB 所有者能够快速了解其 CosmosDB 帐户的性能和利用率问题。
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 05000f4c670860be249d80b0e695a8a8760510be
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797579"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>探究适用于 Azure Cosmos DB 的 Azure Monitor

适用于 Azure Cosmos DB 的 Azure Monitor 提供了关于所有 Azure Cosmos DB 资源的整体性能、故障、容量和运行状况的统一交互式体验视图。 本文将帮助你了解这一新的监视体验的优势，以及如何修改和调整体验以满足你的组织的独特需求。   

## <a name="introduction"></a>简介

在深入了解此体验之前，你应该了解它如何呈现和可视化信息。 

它提供以下功能：

* 在单一位置为你的所有订阅中的 Azure Cosmos DB 资源提供**规模化视角**，并且能够有选择地将评估范围限定于你感兴趣的那些订阅和资源。

* 对特定的 Azure CosmosDB 资源进行**深化分析**，以便诊断问题或按类别（利用率、故障、容量和操作）进行详细分析。 选择这些选项中的任何一个即可深入了解相关的 Azure Cosmos DB 指标。  

* **可自定义** - 此体验是基于 Azure Monitor 工作簿模板构建的，允许你更改要显示的指标，修改或设置与你的限制相符的阈值，然后将其保存到自定义工作簿中。 然后，可以将工作簿中的图表固定到 Azure 仪表板。  

此功能不要求你显式启用或配置任何项，这些 Azure Cosmos DB 指标是默认收集的。

>[!NOTE]
>访问此功能不需任何费用。系统只会对你配置或启用的 Azure Monitor 基本功能收费，如 [Azure Monitor 定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页中所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>查看 Azure Cosmos DB 的利用率和性能指标

若要查看所有订阅中的存储帐户的利用率和性能，请执行以下步骤。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“Monitor”并选择“Monitor”。

    ![搜索框，包含单词“Monitor”和一个下拉框（其中显示了具有速度计样式图像的服务“Monitor”）](./media/cosmosdb-insights-overview/search-monitor.png)

3. 选择“Cosmos DB”。

    ![Cosmos DB 概览工作簿的屏幕截图](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>概述

在“概览”中，表中显示了交互式 Azure Cosmos DB 指标。 可以根据你从下面的下拉列表中选择的选项来筛选结果：

* **订阅** - 仅列出具有 Azure Cosmos DB 资源的订阅。  

* **Cosmos DB** - 可以选择全部、部分或单个 Azure Cosmos DB 资源。

* **时间范围** - 默认情况下，系统会根据你所做的选择显示过去 4 小时的相应信息。

下拉列表下的计数器磁贴汇总了所选订阅中 Azure Cosmos DB 资源的总数。 工作簿中报告事务指标的列存在条件颜色编码或热图。 最深的颜色具有最高值，较浅的颜色基于最低值。 

选择某个 Azure Cosmos DB 资源旁边的下拉箭头时，会在单个数据库容器级别显示性能指标明细：

![展开的下拉框，其中显示了单独的数据库容器和关联的性能明细](./media/cosmosdb-insights-overview/container-view.png)

选择以蓝色突出显示的 Azure Cosmos DB 资源名称会转到关联 Azure Cosmos DB 帐户的默认“概览”。 

### <a name="failures"></a>失败数

在页面顶部选择“故障”，工作簿模板的“故障”部分随即打开。 其中显示了请求总数以及构成这些请求的响应的分布：

![按 HTTP 请求类型细分的故障的屏幕截图](./media/cosmosdb-insights-overview/failures.png)

| 代码      |  说明       | 
|-----------|:--------------------|
| `200 OK`  | 下列 REST 操作之一成功： </br>- 对资源执行 GET。 </br> - 对资源执行 PUT。 </br> - 对资源执行 POST。 </br> - 对某个存储过程资源执行 POST 以执行该存储过程。|
| `201 Created` | 用于创建资源的 POST 操作成功。 |
| `404 Not Found` | 此操作正在尝试对不再存在的资源执行操作。 例如，该资源可能已被删除。 |

有关状态代码的完整列表，请参阅 [Azure Cosmos DB HTTP 状态代码](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)一文。

### <a name="capacity"></a>容量

在页面顶部选择“容量”，工作簿模板的“容量”部分随即打开。 它显示你有多少文档，你的文档随时间推移而增长的情况、数据使用情况以及你剩余的可用存储总量。  这有助于识别潜在的存储和数据利用率问题。

![容量工作簿](./media/cosmosdb-insights-overview/capacity.png) 

与概览工作簿一样，在“订阅”列中选择某个 Azure Cosmos DB 资源旁边的下拉箭头时，会显示组成该数据库的各个容器的明细。

### <a name="operations"></a>操作 

在页面顶部选择“操作”，工作簿模板的“操作”部分随即打开。 可以在其中查看按发出的请求类型细分的请求数。 

因此，在下面的示例中，你会看到 `eastus-billingint` 收到的主要是读取请求，但有少量的更新插入和创建请求。 而从请求的角度来看，`westeurope-billingint` 收到的是只读请求，至少在过去四小时（目前通过工作簿的时间范围参数对工作簿进行的范围设置）是这样。

![操作工作簿](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>固定、导出和展开

可以将任一指标部分固定到 [Azure 仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)，方法是选择该部分右上角的图钉图标。

![仪表板指标部分的图钉示例](./media/cosmosdb-insights-overview/pin.png)

若要将数据导出为 Excel 格式，请选择图钉图标左侧的向下箭头图标。

![工作簿的导出图标](./media/cosmosdb-insights-overview/export.png)

若要展开或折叠工作簿中的所有下拉视图，请选择导出图标左侧的展开图标：

![工作簿的展开图标](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>自定义适用于 Azure Cosmos DB 的 Azure Monitor

因为此体验是基于 Azure Monitor 工作簿模板构建的，所以你能够进行“自定义” > “编辑”，并将修改后版本的副本保存为自定义工作簿。 

![自定义栏](./media/cosmosdb-insights-overview/customize.png)

无论是在私有的“我的报表”部分中，还是在有权访问资源组的每个人均可访问的“共享报表”部分中，工作簿都保存在某个资源组中。 保存自定义工作簿后，需要转到工作簿库来启动它。

![从命令栏启动工作簿库](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)和[服务运行状况通知](../../service-health/alerts-activity-log-service-notifications.md)来设置自动警报，以帮助检测问题。

* 查看[使用 Azure Monitor 工作簿创建交互式报表](../platform/workbooks-overview.md)，了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。
