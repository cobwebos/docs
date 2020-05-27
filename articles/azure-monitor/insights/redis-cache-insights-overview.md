---
title: 适用于 Azure Cache for Redis（预览版）的 Azure Monitor | Microsoft Docs
description: 本文介绍了适用于 Redis Cache 的 Azure Monitor 功能。使用此功能，Azure Cache for Redis 所有者能够快速了解性能和利用率问题。
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210969"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>探究适用于 Azure Cache for Redis（预览版）的 Azure Monitor

适用于 Azure Cache for Redis（预览版）的 Azure Monitor 提供了关于所有 Azure Cache for Redis 资源的整体性能、故障、容量和运行状况的统一交互式体验视图。 本文将帮助你了解这一新的监视体验的优势，以及如何修改和调整体验以满足你的组织的独特需求。

## <a name="introduction"></a>简介

在深入了解此体验之前，你应该了解它如何呈现和可视化信息。

它提供以下功能：

* 在单一位置为你的所有订阅中的 Azure Cache for Redis 资源提供**规模化视角**，并且能够有选择地将评估范围限定于你感兴趣的那些订阅和资源。

* 对特定的 Azure Cache for Redis 资源进行**深化分析**，以便诊断问题或按类别（利用率、故障、容量和操作）进行详细分析。 选择这些选项中的任何一个即可深入了解相关内容。  

* **可自定义** - 此体验是基于 Azure Monitor 工作簿模板构建的，允许你更改要显示的指标，修改或设置与你的限制相符的阈值，然后将其保存到自定义工作簿中。 然后，可以将工作簿中的图表固定到 Azure 仪表板。  

此功能不要求你启用或配置任何项，这些 Azure Cache for Redis 信息是默认收集的。

>[!NOTE]
>访问此功能不需任何费用。系统只会对你配置或启用的 Azure Monitor 基本功能收费，如 [Azure Monitor 定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页中所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>查看 Azure Cache for Redis 的利用率和性能指标

若要查看所有订阅中的存储帐户的利用率和性能，请执行以下步骤。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“Monitor”并选择“Monitor”。

    ![搜索框，包含单词“Monitor”和一个下拉框（其中显示了具有速度计样式图像的服务“Monitor”）](./media/cosmosdb-insights-overview/search-monitor.png)

3. 选择“Azure Cache for Redis (预览版)”。

如果此选项未显示，请单击“更多”并选择“Azure Cache for Redis”。 

### <a name="overview"></a>概述

在“概览”中，表中显示了交互式 Azure Cache for Redis 指标。 可以根据你从下面的下拉列表中选择的选项来筛选结果：

* **订阅** - 仅列出具有 Azure Cache for Redis 资源的订阅。  

* **Azure Cache for Redis** - 可以选择全部、部分或单个 Azure Cache for Redis 资源。

* **时间范围** - 默认情况下，系统会根据你所做的选择显示过去 4 小时的相应信息。

下拉列表下的计数器磁贴汇总了所选订阅中 Azure Cache for Redis 资源的总数。 工作簿中报告事务指标的列存在条件颜色编码或热图。 最深的颜色具有最高值，较浅的颜色基于最低值。

选择某个 Azure Cache for Redis 资源旁边的下拉箭头时，会在个体资源级别显示性能指标明细：

![概览体验的屏幕截图](./media/redis-cache-insights-overview/overview.png)

选择以蓝色突出显示的 Azure Cache for Redis 资源名称会转到关联帐户的默认“概览”。 它显示 `Used Memory`、`Used Memory Percentage`、`Server Load`、`Server Load Timeline`、`CPU`、`Connected Clients`、`Cache Misses`、`Errors (Max)`。

### <a name="operations"></a>操作

在页面顶部选择“操作”，工作簿模板的“操作”部分随即打开。 它显示 `Total Operations`、`Total Operations Timeline`、`Operations Per Second`、`Gets`、`Sets`。

![概览体验的屏幕截图](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>使用情况

在页面顶部选择“使用情况”，工作簿模板的“使用情况”部分随即打开。 它显示 `Cache Read`、`Cache Read Timeline`、`CacheWrite`、`CacheHits`、`Cache Misses`。

![概览体验的屏幕截图](./media/redis-cache-insights-overview/usage.png)

在页面顶部选择“故障”，工作簿模板的“故障”部分随即打开。 它显示 `Total Errors`、`Failover/Errors`、`UnresponsiveClient/Errors`、`RDB/Errors`、`AOF/Errors`、`Export/Errors`、`Dataloss/Errors`、`Import/Errors`。

![按 HTTP 请求类型细分的故障的屏幕截图](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>指标定义

有关构成这些工作簿的指标定义的完整列表，请查看[可用指标和报告时间间隔](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals)一文。

## <a name="pin-export-and-expand"></a>固定、导出和扩展

可以将任一指标部分固定到 [Azure 仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)，方法是选择该部分右上角的图钉图标。

![仪表板指标部分的图钉示例](./media/cosmosdb-insights-overview/pin.png)

若要将数据导出为 Excel 格式，请选择图钉图标左侧的向下箭头图标。

![工作簿的导出图标](./media/cosmosdb-insights-overview/export.png)

若要展开或折叠工作簿中的所有下拉视图，请选择导出图标左侧的展开图标：

![工作簿的展开图标](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>自定义适用于 Azure Cache for Redis（预览版）的 Azure Monitor

因为此体验是基于 Azure Monitor 工作簿模板构建的，所以你能够进行“自定义” > “编辑”，并将修改后版本的副本保存为自定义工作簿。 

![自定义栏](./media/cosmosdb-insights-overview/customize.png)

无论是在私有的“我的报表”部分中，还是在有权访问资源组的每个人均可访问的“共享报表”部分中，工作簿都保存在某个资源组中。 保存自定义工作簿后，需要转到工作簿库来启动它。

![从命令栏启动工作簿库](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)和[服务运行状况通知](../../service-health/alerts-activity-log-service-notifications.md)来设置自动警报，以帮助检测问题。

* 查看[使用 Azure Monitor 工作簿创建交互式报表](../app/usage-workbooks.md)，了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。
