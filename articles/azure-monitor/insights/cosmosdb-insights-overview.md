---
title: 监视 Azure Cosmos DB Azure Monitor Cosmos DB （预览版） |Microsoft Docs
description: 本文介绍 Cosmos DB 功能的 Azure Monitor，该功能提供 Cosmos DB 所有者快速了解 CosmosDB 帐户的性能和使用问题。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 8e265b592bebfc506ae0116c955403dd1070ad3f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166403"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>探索 Azure Cosmos DB Azure Monitor （预览）

Azure Monitor for Azure Cosmos DB （预览版）提供了一个统一的交互式体验，可查看所有 Azure Cosmos DB 资源的总体性能、故障、容量和操作运行状况。 本文将帮助你了解这一新的监视体验的好处，以及如何修改和调整体验以满足你的组织的独特需求。   

## <a name="introduction"></a>简介

在深入体验之前，您应该了解它是如何呈现和可视化信息的。 

它提供：

* 在单个位置的所有订阅中，**大规模了解**你的 Azure Cosmos DB 资源，并可以有选择性地将其范围限定为仅限你需要评估的订阅和资源。

* 向**下钻取**特定 Azure CosmosDB 资源的分析，以帮助诊断问题或按类别执行详细分析：利用率、故障、容量和操作。 选择这些选项中的任何一个可提供相关 Azure Cosmos DB 度量值的深入视图。  

* **自定义**-此体验基于 Azure Monitor 工作簿模板构建，使你能够更改显示的度量值，修改或设置与限制相符的阈值，然后将其保存到自定义工作簿中。 然后，可以将工作簿中的图表固定到 Azure 仪表板。  

此功能不要求你启用或配置任何内容，默认情况下会收集这些 Azure Cosmos DB 度量值。

>[!NOTE]
>访问此功能并不收取任何费用，只需按[Azure Monitor 定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页中所述，为你配置或启用 Azure Monitor 基本功能付费。


## <a name="accessing-azure-monitor-for-azure-cosmos-db"></a>访问 Azure Cosmos DB 的 Azure Monitor

若要查看所有订阅中的存储帐户的利用率和性能，请执行以下步骤。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索 "**监视器**"，然后选择 "**监视器**"。

    ![带有单词 "Monitor" 的搜索框和一个显示速度计样式图像的服务 "Monitor" 的下拉框](./media/cosmosdb-insights-overview/search-monitor.png)

3. 选择**Cosmos DB （预览）** 。

    ![Cosmos DB 概述工作簿的屏幕截图](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>概述

**概述**时，该表显示交互式 Azure Cosmos DB 度量。 您可以根据从下列下拉列表中选择的选项筛选结果：

* 仅列出具有 Azure Cosmos DB 资源**的订阅。**  

* **Cosmos DB** -可以选择 "全部"、"子集" 或 "单个 Azure Cosmos DB 资源"。

* **时间范围**-默认情况下，基于所做的相应选择显示最后4个小时的信息。

下拉列表下的计数器磁贴汇总了所选订阅中 Azure Cosmos DB 资源的总数。 对于报表事务指标的工作簿中的列，有条件颜色编码或热图。 最深的颜色具有最高的值，较浅的颜色基于最低值。 

选择其中一个 Azure Cosmos DB 资源旁边的下拉箭头将显示单个数据库容器级别的性能指标细目：

![展开的下拉菜单显示了单独的数据库容器和关联的性能细目](./media/cosmosdb-insights-overview/container-view.png)

选择以蓝色突出显示的 Azure Cosmos DB 资源名称将转到关联 Azure Cosmos DB 帐户的默认 "**概述**"。 

### <a name="failures"></a>失败数

选择页面顶部的 "**失败**"，工作簿模板的 "**失败**" 部分会打开。 其中显示了组成这些请求的响应分发的总请求数：

![按 HTTP 请求类型分类的故障的屏幕截图](./media/cosmosdb-insights-overview/failures.png)

| 代码      |  描述       | 
|-----------|:--------------------|
| `200 OK`  | 以下 REST 操作之一已成功： </br>-获取资源。 </br> -对资源进行放置。 </br> -发布资源。 </br> -POST，用于执行存储过程的存储过程资源。|
| `201 Created` | 创建资源的 POST 操作成功。 |
| `404 Not Found` | 操作正在尝试对不再存在的资源执行操作。 例如，该资源可能已被删除。 |

有关状态代码的完整列表，请参阅[AZURE COSMOS DB HTTP 状态代码](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)"一文。

### <a name="capacity"></a>Capacity

选择页面顶部的 "**容量**"，此时将打开工作簿模板的**容量**部分。 它向您展示了多少文档、您的文档随着时间推移的增长、数据使用情况以及您剩余的可用存储总量。  这可用于帮助识别潜在的存储和数据利用率问题。

![容量工作簿](./media/cosmosdb-insights-overview/capacity.png) 

与概述工作簿一样，在 "**订阅**" 列中选择 Azure Cosmos DB 资源旁边的下拉列表将显示组成数据库的各个容器的细分。

### <a name="operations"></a>Operations 

选择页面顶部的 "**操作**"，将打开工作簿模板的 "**操作**" 部分。 它使你能够查看按发出的请求类型细分的请求。 

因此，在下面的示例中，你将看到 `eastus-billingint` 主要接收读取请求，但使用少量的 upsert 和创建请求。 而从请求的角度来看，`westeurope-billingint` 是只读的，并且在过去四小时内，工作簿当前通过其时间范围参数进行了作用域。

![操作工作簿](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>固定、导出和扩展

您可以通过选择部分右上角的图钉图标将任一指标部分固定到[Azure 仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)。

![指标部分固定到仪表板示例](./media/cosmosdb-insights-overview/pin.png)

若要将数据导出到 Excel 格式，请选择图钉图标左侧的向下箭头图标。

![导出工作簿图标](./media/cosmosdb-insights-overview/export.png)

若要展开或折叠工作簿中的所有下拉视图，请选择 "导出" 图标左侧的展开图标：

![展开工作簿图标](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>自定义 Azure Cosmos DB 的 Azure Monitor （预览）

由于此体验是基于 Azure Monitor 工作簿模板构建的，因此您可以**自定义** > **编辑**已修改版本的副本并**将其保存**到自定义工作簿中。 

![自定义栏](./media/cosmosdb-insights-overview/customize.png)

工作簿保存在资源组中，无论是在专用于你的**我的报表**节中，还是在有权访问资源组的每个人均可访问的 "**共享报表**" 部分中。 保存自定义工作簿后，需要先进入工作簿库以启动它。

![从命令栏启动工作簿库](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)和[服务运行状况通知](../../service-health/alerts-activity-log-service-notifications.md)，设置自动警报，以帮助检测问题。

* 了解工作簿设计为支持的方案、如何创作新报表和自定义现有报表，以及如何通过查看[使用 Azure Monitor 工作簿创建交互式报表](../app/usage-workbooks.md)。
