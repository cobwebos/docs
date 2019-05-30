---
title: 监视 Azure Monitor 中的数据位置 |Microsoft Docs
description: 介绍允许监视数据存储在 Azure 包括 Azure Monitor 数据平台的不同位置。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 61f34277a03deb312e93920e3bd76ce5297f020f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357545"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>在 Azure Monitor 监视数据位置

Azure 监视器基于[的数据平台](data-platform.md)的[日志](data-platform-logs.md)并[指标](data-platform-metrics.md)中所述[Azure Monitor 数据平台](data-platform.md)。 监视 Azure 资源中的数据可能会写入到其他位置，或者它们被复制到 Azure Monitor 或以支持其他方案前。 

## <a name="monitoring-data-locations"></a>监视数据位置

下表标识了在 Azure 中监视数据发送到的位置的不同位置以及对其进行访问的不同方法。

| Location | 描述 | 访问方法 |
|:---|:---|:---|:--|
| Azure Monitor 指标 | 时间序列数据库针对分析加盖时间戳数据进行了优化。 | [Metrics Explorer](metrics-getting-started.md)<br>[Azure Monitor 指标 API](/rest/api/monitor/metrics) |
| Azure Monitor 日志    | Log Analytics 工作区的基于 Azure 的数据资源管理器提供功能强大的分析引擎和丰富的查询语言。 | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 活动日志 | 活动日志中的数据发送到 Azure Monitor 日志，以便与其他数据，对其进行分析时非常有用，但它还将收集在其自身以便在 Azure 门户中可以直接查看。 | [Azure 门户](activity-log-view.md#azure-portal)<br>[Azure Monitor 事件 API](/rest/api/monitor/eventcategories) |
| Azure 存储 | 某些数据源将直接写入 Azure 存储，需要配置以将数据移动到日志。 此外可以将数据发送到 Azure 存储用于存档以及与外部系统的集成。  | [存储分析](/rest/api/storageservices/storage-analytics)<br>[服务器资源管理器](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[存储资源管理器](/visualstudio/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| 事件中心 | 将数据发送到 Azure 事件中心流式传输到其他位置。 | [捕获到存储](../../event-hubs/event-hubs-capture-overview.md)  |
| 用于 VM 的 Azure Monitor | Azure 监视的 Vm 将工作负荷的运行状况数据存储在由其在 Azure 门户中的监视体验的自定义位置。 | [Azure 门户](../insights/vminsights-overview.md)<br>[工作负荷监视器 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 资源运行状况 REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 警报 | 所创建的 Azure Monitor 的警报。 | [Azure 门户](alerts-managing-alert-instances.md)<br>[警报管理 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>后续步骤

- 请参阅不同的源[由 Azure Monitor 监视数据收集](data-sources.md)。
- 了解如何[类型的监视数据由 Azure Monitor 收集](data-platform.md)以及如何查看和分析此数据。
