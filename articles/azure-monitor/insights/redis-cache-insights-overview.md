---
title: 适用于 Redis 的 Azure Cache Azure Monitor |Microsoft Docs
description: 本文介绍了适用于 Azure Redis Cache 的 Azure Monitor 功能。使用此功能，缓存所有者能够快速了解性能和利用率问题。
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/10/2020
ms.openlocfilehash: e7b8a8e131cf7e0aae051f42043abf0640c927e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90887009"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>浏览用于 Redis 的 Azure Cache Azure Monitor

对于所有适用于 Redis 资源的 Azure 缓存，适用于 Redis 的 Azure Cache Azure Monitor 提供了一个统一的交互式视图，其中提供了：

- 总体性能
- 失败数
- 容量
- 操作运行状况

本文有助于了解这种新的监视体验的优势。 它还演示了如何修改和调整该体验，以满足组织的独特需求。

## <a name="introduction"></a>简介

在开始体验之前，应先了解适用于 Azure Cache for Redis 的 Azure Monitor 如何直观显示信息。

它提供以下功能：

- 在一个位置大规模查看所有订阅中的 Azure Cache for Redis 资源。 可以选择将范围限制为仅要评估的订阅和资源。

- 对特定的 Azure Cache for Redis 资源进行深化分析。 可以诊断问题并查看利用率、失败、容量和操作的详细分析。 选择其中的任何类别以深入了解相关信息。  

- 自定义此体验，它基于 Azure Monitor 工作簿模板构建。 通过此体验，可以更改显示的指标，并修改或设置与限制一致的阈值。 可以将更改保存在自定义工作簿中，然后将工作簿图表固定到 Azure 仪表板。

此功能不要求启用或配置任何内容。 默认收集 Azure Cache for Redis 信息。

>[!NOTE]
>使用此功能不收取任何费用。 系统只会对你配置或启用的 Azure Monitor 基本功能收费，如 [Azure Monitor 定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页中所述。

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>查看 Azure Cache for Redis 的利用率和性能指标

若要查看所有订阅中的存储帐户的利用率和性能，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 搜索“Monitor”并选择“Monitor” 。

    ![搜索框显示单词“Monitor”，“服务”搜素结果显示了具有速度计符号的“Monitor”](./media/cosmosdb-insights-overview/search-monitor.png)

1. **为 Redis 选择 "Azure 缓存**"。 如果此选项不存在，请选择“更多” > “Azure Cache for Redis” 。

### <a name="overview"></a>概述

在“概览”中，表中显示了交互式 Azure Cache for Redis 指标。 可以根据你从下面的下拉列表中选择的选项来筛选结果：

- **订阅**：仅列出具有 Azure Cache for Redis 资源的订阅。  

- **Azure Cache for Redis**：可以选择全部、部分或单个 Azure Cache for Redis 资源。

- **时间范围**：默认情况下，表会根据相应选择显示过去四小时的信息。

下拉列表下有一个计数器磁贴。 该磁贴显示所选订阅中 Azure Cache for Redis 资源的总数。 工作簿列的条件颜色代码或热度地图报告事务指标。 最深的颜色表示最高值。 较浅的颜色表示较低的值。

选择某个 Azure Cache for Redis 资源旁边的下拉列表箭头时，会在单个资源级别显示性能指标明细。

![概览体验的屏幕截图](./media/redis-cache-insights-overview/overview.png)

当选择以蓝色突出显示 Azure Cache for Redis 资源名称，会看到关联帐户的默认“概述”表。 其中显示了以下列：

- 已用内存
- 已用内存百分比
- 服务器负载
- 服务器负载时间线
- CPU
- 连接的客户端数
- 缓存未命中数
- 错误数(最大值)

### <a name="operations"></a>操作

选择页面顶部的“操作”时，工作簿模板的“操作”表随即打开 。 其中显示了以下列：

- 总操作数
- 总操作数时间线
- 每秒操作数
- 获取数
- 设置数

![操作体验的屏幕截图](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>使用情况

选择页面顶部的“使用情况”时，工作簿模板的“使用情况”表随即打开 。 其中显示了以下列：

- 缓存读取量
- 缓存读取量时间线
- 缓存写入量
- 缓存命中数
- 缓存未命中数

![使用情况体验的屏幕截图](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>失败数

选择页面顶部的“故障”时，工作簿模板的“故障”表随即打开 。 其中显示了以下列：

- 错误总数
- 故障转移/错误数
- UnresponsiveClient/错误数
- RDB/错误数
- AOF/错误数
- 导出/错误数
- 数据丢失/错误数
- 导入/错误数

![按 HTTP 请求类型细分的故障的屏幕截图](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>指标定义

有关构成这些工作簿的指标定义的完整列表，请查看[可用指标和报告时间间隔](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals)一文。

## <a name="view-from-an-azure-cache-for-redis-resource"></a>从 Azure Cache for Redis 资源查看

若要直接从单个资源访问适用于 Azure Cache for Redis 的 Azure Monitor，请执行以下操作：

1. 在 Azure 门户中，选择 Azure Cache for Redis。

2. 从列表中，选择单个 Azure Cache for Redis 资源。 在 "监视" 部分，选择 "见解"。

    ![红色框中突出显示了 "Insights" 字样的菜单选项的屏幕截图](./media/redis-cache-insights-overview/insights.png)

还可以通过从 Azure Monitor 级工作簿中选择 Azure Cache for Redis 资源的资源名称来访问这些视图。

### <a name="resource-level-overview"></a>资源级概述

在 Azure Redis Cache 的“概述”工作簿中，显示了多个可供访问以下内容的性能指标：

- 交互式性能图表，其中显示与 Azure Cache for Redis 性能相关的最重要的详细信息。

- 指标和状态磁贴，突出显示了分片性能、连接的客户端总数以及整体延迟。

![“概述”仪表板的屏幕截图，其中显示有关 CPU 性能、已用内存、连接的客户端数、错误数、过期密钥数和逐出的密钥数的信息](./media/redis-cache-insights-overview/resource-overview.png)

选择“性能”或“操作”对应的任何其他选项卡会打开相应的工作簿 。

### <a name="resource-level-performance"></a>资源级性能

![资源性能图的屏幕截图](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>资源级操作

![资源操作图的屏幕截图](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>固定、导出和展开

若要将任何指标部分固定到 [Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)，请选择该部分右上角的图钉符号。

![突出显示图钉符号的指标部分](./media/cosmosdb-insights-overview/pin.png)

若要将数据导出为 Excel 格式，请选择图钉符号左侧的向下箭头符号。

![突出显示的导出工作簿符号](./media/cosmosdb-insights-overview/export.png)

若要展开或折叠工作簿中的所有视图，请选择导出符号左侧的展开符号。

![突出显示的展开工作簿符号](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>自定义 Redis 的 Azure Cache Azure Monitor

因为此体验是基于 Azure Monitor 工作簿模板构建的，所以可以选择“自定义” > “编辑” > 保存”，将修改后版本的副本保存为自定义工作簿  。

![突出显示“自定义”的命令栏](./media/cosmosdb-insights-overview/customize.png)

工作簿保存在“我的报表”部分或“共享报表”部分的资源组中 。 “我的报表”仅供你使用。 “共享报表”可供有权访问资源组的每个人使用。

保存自定义工作簿后，转到工作簿库来打开它。

![突出显示“库”的命令栏](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>故障排除

如需故障排查指南，请参阅[故障排除文章](troubleshoot-workbooks.md)中提供的基于工作簿的专用见解。

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)和[服务运行状况通知](../../service-health/alerts-activity-log-service-notifications-portal.md)来设置自动警报，以帮助检测问题。

* 查看[使用 Azure Monitor 工作簿创建交互式报表](../platform/workbooks-overview.md)，了解工作簿支持的方案、创作和自定义报表的方式，以及更多信息。
