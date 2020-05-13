---
title: 适用于 Redis 的 Azure 缓存的 Azure Monitor （预览版） |Microsoft Docs
description: 本文介绍 Redis 缓存功能的 Azure Monitor，该功能为 Redis 所有者提供 Azure Cache，并快速了解性能和利用率问题。
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210969"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>了解适用于 Redis （预览版）的 Azure Cache Azure Monitor

适用于 Redis （预览版）的 Azure Cache Azure Monitor 提供了一种统一的交互式体验，为 Redis 资源的所有 Azure 缓存提供总体性能、故障、容量和操作运行状况。 本文将帮助你了解这一新的监视体验的好处，以及如何修改和调整体验以满足你的组织的独特需求。

## <a name="introduction"></a>简介

在深入体验之前，您应该了解它是如何呈现和可视化信息的。

它提供：

* 在单个位置的所有订阅中，**大规模**了解 Azure Cache for Redis 资源，并可以有选择性地范围限定为要评估的订阅和资源。

* **向下钻取**对特定 Azure Cache for Redis 资源的分析，以帮助诊断问题或按类别、故障、容量和操作执行详细分析。 选择这些选项中的任何一个可提供相关的深入视图。  

* **自定义**-此体验基于 Azure Monitor 工作簿模板构建，使你能够更改显示的度量值，修改或设置与限制相符的阈值，然后将其保存到自定义工作簿中。 然后，可以将工作簿中的图表固定到 Azure 仪表板。  

此功能不要求你启用或配置任何操作，默认情况下会收集这些适用于 Redis 的 Azure 缓存。

>[!NOTE]
>访问此功能并不收取任何费用，只需按[Azure Monitor 定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页中所述，为你配置或启用 Azure Monitor 基本功能付费。

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>查看适用于 Redis 的 Azure 缓存的利用率和性能指标

若要查看所有订阅中的存储帐户的利用率和性能，请执行以下步骤。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索 "**监视器**"，然后选择 "**监视器**"。

    ![带有单词 "Monitor" 的搜索框和一个显示速度计样式图像的服务 "Monitor" 的下拉框](./media/cosmosdb-insights-overview/search-monitor.png)

3. 选择 "**用于 Redis 的 Azure 缓存（预览版）**"。

如果此选项不存在，请单击 "**更多**"，然后选择 " **Azure Cache for Redis**"。

### <a name="overview"></a>概述

**概述**时，该表显示 Redis 指标的交互式 Azure 缓存。 您可以根据从下列下拉列表中选择的选项筛选结果：

* 仅列出具有 Azure Cache for Redis 资源**的订阅。**  

* **适用于 Redis 的 Azure 缓存**-可以选择 "全部"、一个子集或单个 Azure Cache for Redis 资源。

* **时间范围**-默认情况下，基于所做的相应选择显示最后4个小时的信息。

下拉列表下的计数器磁贴汇总了所选订阅中的 Redis 资源的 Azure 缓存总数。 对于报表事务指标的工作簿中的列，有条件颜色编码或热图。 最深的颜色具有最高的值，较浅的颜色基于最低值。

选择一个用于 Redis 资源的 Azure 缓存旁边的下拉箭头将显示单个资源级别的性能指标细目：

![概述体验的屏幕截图](./media/redis-cache-insights-overview/overview.png)

选择以蓝色突出显示的 Redis 资源名称的 Azure Cache 将转到关联帐户的默认**概述**。 其中显示了、、、 `Used Memory` `Used Memory Percentage` `Server Load` `Server Load Timeline` 、 `CPU` 、、 `Connected Clients` `Cache Misses` 和 `Errors (Max)` 。

### <a name="operations"></a>操作

选择页面顶部的 "**操作**"，将打开工作簿模板的 "**操作**" 部分。 其中显示了、、、 `Total Operations` `Total Operations Timeline` `Operations Per Second` `Gets` 和 `Sets` 。

![概述体验的屏幕截图](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>使用情况

选择页面顶部的 "**使用情况**"，将打开工作簿模板的 "**使用情况**" 部分。 其中显示了、、、 `Cache Read` `Cache Read Timeline` `CacheWrite` `CacheHits` 和 `Cache Misses` 。

![概述体验的屏幕截图](./media/redis-cache-insights-overview/usage.png)

选择页面顶部的 "**失败**"，工作簿模板的 "**失败**" 部分会打开。 其中显示了、、、 `Total Errors` `Failover/Errors` `UnresponsiveClient/Errors` `RDB/Errors` 、 `AOF/Errors` 、、 `Export/Errors` `Dataloss/Errors` 和 `Import/Errors` 。

![按 HTTP 请求类型分类的故障的屏幕截图](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>指标定义

有关构成这些工作簿的指标定义的完整列表，请参阅[可用度量值和报告间隔一文](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals)。

## <a name="pin-export-and-expand"></a>固定、导出和扩展

您可以通过选择部分右上角的图钉图标将任一指标部分固定到[Azure 仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)。

![指标部分固定到仪表板示例](./media/cosmosdb-insights-overview/pin.png)

若要将数据导出到 Excel 格式，请选择图钉图标左侧的向下箭头图标。

![导出工作簿图标](./media/cosmosdb-insights-overview/export.png)

若要展开或折叠工作簿中的所有下拉视图，请选择 "导出" 图标左侧的展开图标：

![展开工作簿图标](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>为 Redis （预览版）的 Azure Cache 自定义 Azure Monitor

由于此体验是基于 Azure Monitor 工作簿模板构建的，因此您可以**自定义**  >  **编辑**并**将**修改后的版本的副本保存到自定义工作簿中。 

![自定义栏](./media/cosmosdb-insights-overview/customize.png)

工作簿保存在资源组中，无论是在专用于你的**我的报表**节中，还是在有权访问资源组的每个人均可访问的 "**共享报表**" 部分中。 保存自定义工作簿后，需要先进入工作簿库以启动它。

![从命令栏启动工作簿库](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)和[服务运行状况通知](../../service-health/alerts-activity-log-service-notifications.md)，设置自动警报，以帮助检测问题。

* 了解工作簿设计为支持的方案、如何创作新报表和自定义现有报表，以及如何通过查看[使用 Azure Monitor 工作簿创建交互式报表](../app/usage-workbooks.md)。
