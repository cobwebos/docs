---
title: 使用 Azure 监视器监视宇宙数据库（预览）*微软文档
description: 本文介绍了 Cosmos DB 的 Azure 监视器功能，该功能使 Cosmos DB 所有者能够快速了解其 CosmosDB 帐户的性能和利用率问题。
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250679"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>浏览 Azure 宇宙数据库的 Azure 监视器（预览）

Azure Cosmos DB 的 Azure 监视器（预览版）在统一的交互式体验中提供了所有 Azure Cosmos DB 资源的总体性能、故障、容量和操作运行状况的视图。 本文将帮助您了解此新的监视体验的好处，以及如何修改和调整体验以适应组织的独特需求。   

## <a name="introduction"></a>介绍

在深入了解体验之前，您应该了解它如何呈现和可视化信息。 

它提供：

* 从 Azure Cosmos DB 资源**缩放到**单个位置的所有订阅，并能够有选择地仅扩展到您感兴趣的订阅和资源。

* **向下钻取**对特定 Azure CosmosDB 资源的分析，以帮助诊断问题或按类别执行详细分析 - 利用率、故障、容量和操作。 选择其中任一选项可深入了解相关的 Azure Cosmos DB 指标。  

* **可自定义**- 此体验基于 Azure 监视器工作簿模板构建，允许您更改显示的指标，修改或设置符合限制的阈值，然后保存到自定义工作簿中。 然后，可以将工作簿中的图表固定到 Azure 仪表板。  

此功能不要求您启用或配置任何内容，默认情况下将收集这些 Azure Cosmos DB 指标。

>[!NOTE]
>访问此功能不收取任何费用，并且只需为配置或启用的 Azure 监视器基本功能付费，如[Azure 监视器定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页上所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>查看 Azure 宇宙数据库的利用率和性能指标

要查看存储帐户在所有订阅中的利用率和性能，请执行以下步骤。

1. 登录到 Azure[门户](https://portal.azure.com)。

2. 搜索**监视器**并选择**监视器**。

    ![带有"监视器"字样的搜索框和显示服务"监视器"且速度计样式图像的下拉列表](./media/cosmosdb-insights-overview/search-monitor.png)

3. 选择**宇宙数据库（预览）。**

    ![宇宙数据库概述工作簿的屏幕截图](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>概述

在**概述**上，该表显示交互式 Azure 宇宙数据库指标。 您可以根据从以下下拉列表中选择的选项筛选结果：

* **订阅**- 仅列出具有 Azure Cosmos DB 资源的订阅。  

* **宇宙数据库**- 您可以选择所有、子集或单个 Azure Cosmos 数据库资源。

* **时间范围**- 默认情况下，根据所做的相应选择显示最后 4 小时的信息。

下拉列表下的计数器磁贴将 Azure Cosmos DB 资源的总数汇总到所选订阅中。 工作簿中报告交易记录指标的列有条件颜色编码或热图。 最深的颜色具有最高值，较浅的颜色基于最低值。 

在 Azure Cosmos 数据库资源之一旁边选择下拉箭头将显示单个数据库容器级别的性能指标的细目：

![展开下拉列表，显示单个数据库容器和相关性能细分](./media/cosmosdb-insights-overview/container-view.png)

选择以蓝色突出显示的 Azure Cosmos DB 资源名称将带您到关联的 Azure Cosmos DB 帐户的默认 **"概述**"。 

### <a name="failures"></a>失败数

选择页面顶部的 **"失败"，** 并打开工作簿模板的 **"失败"** 部分。 它显示包含构成这些请求的响应分布的总请求总数：

![按 HTTP 请求类型细分的失败屏幕截图](./media/cosmosdb-insights-overview/failures.png)

| 代码      |  描述       | 
|-----------|:--------------------|
| `200 OK`  | 下列 REST 操作之一成功： </br>- 获取资源。 </br> - 在资源上打开。 </br> - 在资源上打开 POST。 </br> - 在存储过程资源上 POST 以执行存储过程。|
| `201 Created` | 创建资源的 POST 操作成功。 |
| `404 Not Found` | 操作正在尝试处理一个不再存在的资源。 例如，该资源可能已删除。 |

有关状态代码的完整列表，请参阅 Azure[宇宙 DB HTTP 状态代码一文](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)。

### <a name="capacity"></a>容量

选择页面顶部的 **"容量"，** 然后打开工作簿模板的 **"容量**"部分。 它显示您拥有的文档数、文档随时间的增长、数据使用情况以及您留下的可用存储总量。  这可用于帮助确定潜在的存储和数据利用率问题。

![容量工作簿](./media/cosmosdb-insights-overview/capacity.png) 

与概述工作簿一样，在 **"订阅"** 列中选择 Azure Cosmos DB 资源旁边的下拉列表将显示构成数据库的各个容器的细目。

### <a name="operations"></a>操作 

选择页面顶部的 **"操作"，** 然后打开工作簿模板的 **"操作**"部分。 它使您能够查看按请求类型细分的请求。 

因此，在下面的示例中，您可以看到主要`eastus-billingint`接收读取请求，但使用少量 upsert 和创建请求。 而`westeurope-billingint`从请求的角度来看是只读的，但至少在过去四个小时中，工作簿当前通过其时间范围参数被限定为。

![操作工作簿](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>固定、导出和扩展

您可以通过选择分区右上角的图钉图标，将任一指标部分固定到[Azure 仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)。

![指标节针到仪表板示例](./media/cosmosdb-insights-overview/pin.png)

要将数据导出到 Excel 格式，请选择图钉图标左侧的向下箭头图标。

![导出工作簿图标](./media/cosmosdb-insights-overview/export.png)

要展开或折叠工作簿中的所有下拉视图，请选择导出图标左侧的展开图标：

![展开工作簿图标](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>自定义 Azure 宇宙数据库的 Azure 监视器（预览）

由于此体验是在 Azure 监视器工作簿模板之上构建的，因此您可以**自定义** > **编辑**并将修改后的版本的副本**保存到**自定义工作簿中。 

![自定义栏](./media/cosmosdb-insights-overview/customize.png)

工作簿保存在资源组中，无论是在您专用的"**我的报告"** 部分中，还是"**共享报表**"部分中，有权访问资源组的每个人都可以访问。 保存自定义工作簿后，需要转到工作簿库来启动它。

![从命令栏启动工作簿库](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)[和服务运行状况通知](../../service-health/alerts-activity-log-service-notifications.md)以设置自动警报以帮助检测问题。

* 了解工作簿旨在支持的方案、如何通过查看[使用 Azure 监视器工作簿创建交互式报表](../app/usage-workbooks.md)来编写新报表和自定义现有报表等。
