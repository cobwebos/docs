---
title: Azure Monitor 指标图表示例
description: 了解如何可视化 Azure Monitor 数据。
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 00935762a5e19ec47074021aff59992fd3b801bf
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797448"
---
# <a name="metric-chart-examples"></a>指标图表示例 

Azure 平台提供了[一千多个指标](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)，其中许多指标具有维度。 通过使用[维度筛选器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)、应用[拆分](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)、控制图表类型和调整图表设置，可以创建功能强大的诊断视图和仪表板，以深入了解基础结构和应用程序的运行状况。 本文介绍了一些可以使用[指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)生成的图表示例，并说明了配置每个图表的必要步骤。

想和全世界分享你出色的图表示例吗？ 在 GitHub 上参与撰写此页面，并在此处分享你自己的图表示例！

## <a name="website-cpu-utilization-by-server-instances"></a>按服务器实例划分的网站 CPU 利用率

此图表显示应用服务的 CPU 是否在可接受范围内，并按实例对其进行分解，以确定负载是否得到正确分发。 可以从图表上看到，在上午 6 点之前，应用在单个服务器实例上运行，然后通过添加另一个实例进行纵向扩展。

![按服务器实例划分的平均 CPU 百分比折线图](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>如何配置此图表？

选择应用服务资源，并找到“CPU 百分比”  指标。 然后单击“应用拆分”  ，并选择“实例”  维度。

## <a name="application-availability-by-region"></a>各个区域的应用程序可用性

查看各个区域的应用程序可用性，以确定哪些地理位置遇到问题。 此图表显示 Application Insights 可用性指标。 可以看到，受监视的应用程序没有遇到来自美国东部数据中心的可用性问题，但它正在经历来自美国西部和东亚的部分可用性问题。

![各个位置的平均可用性图表](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>如何配置此图表？

首先需要启用网站的 [Application Insights 可用性](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability)监视。 在此之后，选择 Application Insights 资源并选择可用性指标。 在“运行位置”  维度上应用拆分。

## <a name="volume-of-storage-account-transactions-by-api-name"></a>按 API 名称划分的存储帐户事务量

存储帐户资源遇到了过多事务量。 可以使用事务指标来确定哪个 API 负责过多负载。 请注意，下面的图表在筛选和拆分中配置了相同维度（API 名称），以将视图缩小到只包含感兴趣的 API 调用：

![API 事务条形图](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>如何配置此图表？

在指标选取器中，选择你的存储帐户和“事务”  指标。 将图表类型切换为“条形图”  。 单击“应用拆分”  并选择“API 名称”  维度。 然后单击“添加筛选器”  ，再次选择“API 名称”  维度。 在筛选器对话框中，选择要在图表上显示的 API。

## <a name="next-steps"></a>后续步骤

* 了解有关 Azure Monitor [工作簿](../../azure-monitor/platform/workbooks-overview.md)的信息
* 详细了解[指标资源管理器](metrics-charts.md)
