---
title: 监视 Azure Monitor 中的数据位置 |Microsoft Docs
description: 描述在 Azure 中存储监视数据的不同位置，包括 Azure Monitor 数据平台。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666609"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>监视 Azure Monitor 中的数据位置

Azure Monitor 基于[Azure Monitor 数据平台](data-platform.md)中所述的[日志](data-platform-logs.md)和[指标](data-platform-metrics.md)的[数据平台](data-platform.md)。 不过，可以在将 Azure 资源的数据复制到 Azure Monitor 或支持其他方案之前，将这些数据写入到其他位置。 

## <a name="monitoring-data-locations"></a>监视数据位置

下表列出了 Azure 中的监视数据发送到的不同位置以及用于访问该数据的不同方法。

| 位置 | 说明 | 访问方法 |
|:---|:---|:---|:--|
| Azure Monitor 度量值 | 针对分析时间戳数据而优化的时序数据库。 | [指标资源管理器](metrics-getting-started.md)<br>[Azure Monitor 指标 API](/rest/api/monitor/metrics) |
| Azure Monitor 日志    | 基于 Azure 数据资源管理器 Log Analytics 工作区，该工作区提供强大的分析引擎和丰富的查询语言。 | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 活动日志 | 向 Azure Monitor 日志发送数据时，活动日志中的数据最有用，但它也会自行收集，因此可以直接在 Azure 门户中查看。 | [Azure 门户](activity-log-view.md#azure-portal)<br>[Azure Monitor 事件 API](/rest/api/monitor/eventcategories) |
| Azure 存储 | 某些数据源将直接写入 Azure 存储，并需要配置才能将数据移动到日志中。 你还可以将数据发送到 Azure 存储以进行存档，并与外部系统集成。  | [存储分析](/rest/api/storageservices/storage-analytics)<br>[服务器资源管理器](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[存储资源管理器](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| 事件中心 | 将数据发送到 Azure 事件中心，以将数据流式传输到其他位置。 | [捕获到存储](../../event-hubs/event-hubs-capture-overview.md)  |
| 用于 VM 的 Azure Monitor | 用于 VM 的 Azure Monitor 将工作负荷运行状况数据存储在自定义位置，该位置由其在 Azure 门户中的监视体验所使用。 | [Azure 门户](../insights/vminsights-overview.md)<br>[工作负荷监视器 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 资源运行状况 REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 警报 | Azure Monitor 创建的警报。 | [Azure 门户](alerts-managing-alert-instances.md)<br>[警报管理 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>后续步骤

- 查看[Azure Monitor 收集的监视数据](data-sources.md)的不同源。
- 了解[Azure Monitor 收集的监视数据的类型](data-platform.md)，以及如何查看和分析此数据。
