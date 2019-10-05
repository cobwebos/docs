---
title: Azure Monitor 中的监视数据位置 | Microsoft Docs
description: 介绍 Azure 中存储监视数据的不同位置，包括 Azure Monitor 数据平台。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 6f6071bc1d3d7514d22658a07810690ec8a8056b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972762"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Azure Monitor 中的监视数据位置

Azure Monitor 基于[日志](data-platform-logs.md)和[指标](data-platform-metrics.md)的[数据平台](data-platform.md)，如 [Azure Monitor 数据平台](data-platform.md)中所述。 但是，来自 Azure 资源的监视数据可能会写入其他位置，或者在它们复制到 Azure Monitor 之前，或者为了支持其他方案。 

## <a name="monitoring-data-locations"></a>监视数据位置

下表列出了 Azure 中的监视数据发送到的不同位置以及访问这些数据的不同方法。

| Location | 描述 | 访问方法 |
|:---|:---|:---|:--|
| Azure Monitor 指标 | 为分析时间戳数据而优化的时序数据库。 | [指标资源管理器](metrics-getting-started.md)<br>[Azure Monitor 指标 API](/rest/api/monitor/metrics) |
| Azure Monitor 日志    | 基于 Azure 数据资源管理器的 Log Analytics 工作区，它提供了功能强大的分析引擎和丰富的查询语言。 | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 活动日志 | 当将活动日志中的数据发送到 Azure Monitor 日志，以便与其他数据一起分析它时，活动日志中的数据最有用，但活动日志本身也会收集数据，因此可以在 Azure 门户中直接查看活动日志。 | [Azure 门户](activity-log-view.md#azure-portal)<br>[Azure Monitor 事件 API](/rest/api/monitor/eventcategories) |
| Azure 存储 | 一些数据源将直接写入 Azure 存储，并且需要配置才能将数据移到日志中。 还可以将数据发送到 Azure 存储以进行存档以及与外部系统集成。  | [存储分析](/rest/api/storageservices/storage-analytics)<br>[服务器资源管理器](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[存储资源管理器](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| 事件中心 | 将数据发送到 Azure 事件中心以将其流式传输到其他位置。 | [捕获到存储](../../event-hubs/event-hubs-capture-overview.md)  |
| 用于 VM 的 Azure Monitor | 用于 VM 的 Azure Monitor 将工作负荷运行状况数据存储在 Azure 门户中其监视体验所使用的自定义位置。 | [Azure 门户](../insights/vminsights-overview.md)<br>[工作负荷监视器 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 资源运行状况 REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 警报 | Azure Monitor 创建的警报。 | [Azure 门户](alerts-managing-alert-instances.md)<br>[警报管理 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>后续步骤

- 查看 [Azure Monitor 收集的监视数据](data-sources.md)的不同来源。
- 了解 [Azure Monitor 收集的监视数据的类型](data-platform.md)以及如何查看和分析此数据。
