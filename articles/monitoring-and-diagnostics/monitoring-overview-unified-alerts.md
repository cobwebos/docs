---
title: 了解 Azure Monitor 中的全新警报体验 | Microsoft Docs
description: 了解如何利用 Azure 中这种全新的既简单又可缩放的警报体验，简化警报的创作、查看和管理
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: 16e0fc493a257504e2708336e05c30b36d4bea15
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Azure Monitor 中的全新警报体验

## <a name="overview"></a>概述

> [!NOTE]
> 本文介绍更新的警报。 [经典警报概述](monitoring-overview-alerts.md)中介绍了旧式经典 Azure Monitor 警报。 
>
>

警报具有新体验。 旧版警报体验现在在“警报(经典)”选项卡下提供。全新警报体验与“警报(经典)”体验相比具有以下优点：

-   **更好的通知系统**：所有新型警报均使用[操作组]( https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups)，这些组是命名的通知和操作组，可以在多个警报中重复使用。  经典指标警报和旧版 Log Analytics 警报不使用操作组。 
- **统一的创作体验** - 针对 Azure Monitor、Log Analytics 和 Application Insights 中的指标、日志和活动日志的所有警报创建均在一个位置进行。 
- **在 Azure 门户中查看触发的 Log Analytics 警报** - 现在还可以在订阅中查看触发的 Log Analytics 警报。 以前这些警报在单独的门户中。 
- **分开触发的警报和警报规则** - 对警报规则（定义触发警报的条件）和触发的警报（警报规则触发实例）进行了区分，因此操作视图和配置视图是分开的。
- **更好的工作流** - 全新警报创作体验引导用户完成警报规则配置过程，因此发现需要发出警报的事项变得更容易了。
 
具体而言，更新指标警报有了以下改进：
-   **延迟降低**：新型指标警报的运行频率可达每分钟一次。 旧式指标警报每 5 分钟方可运行 1 次。 由于引入日志需要时间，日志警报的延迟仍然超过 1 分钟。 
-   **支持多维指标**：支持对维度指标发出警报，从而可监视受关注的指标段。
-   **更好地控制指标条件**：可以定义更丰富的警报规则。 新型警报支持监视指标的最大值、最小值、平均值和总值。
-   **综合监视多个指标**：可以使用单个规则监视多个指标（目前最多为两个指标）。 如果两个指标在指定时间段内违反其各自的阈值，则会触发警报。
-   **日志中的指标**（受限公共预览版）：进入 Log Analytics 的某些日志数据现在可以提取并转换为 Azure Monitor 指标，然后就像其他指标一样，基于其发出警报。 



以下部分更详细地介绍了新体验的工作原理。

## <a name="alert-rules-terminology"></a>警报规则术语
在跨不同的警报类型统一创作体验时，全新警报体验使用以下概念来区分警报规则和触发的警报对象。

- **目标资源** - 目标可以是任何 Azure 资源。 目标资源定义适用于警报的范围和信号。 示例目标：虚拟机、存储帐户、虚拟机规模集、Log Analytics 工作区或 Application Insights 资源。

- **条件** - 条件是应用于目标资源的信号和逻辑的组合。 示例：百分比 CPU > 70%、服务器响应时间 > 4 毫秒、日志查询的结果计数 > 100，等等。 

- **信号** - 信号由目标资源发出，可以有多种类型。 指标、活动日志、Application Insights 和日志为支持的信号类型。

- **逻辑** - 用户定义逻辑，用于检查信号是否在预期的范围/值内。  
 
- **操作** - 触发警报时执行的特定操作。 例如，通过电子邮件发送电子邮件地址或调用 Webhook URL。 触发警报时，可以执行多个操作。 这些警报支持操作组。  
 
- **警报规则** - 会触发警报的条件。 警报规则捕获警报的目标和条件。 警报规则可以是“已启用”或“已禁用”状态。
 
    > [!NOTE]
    > 这不同于“警报(经典)”体验。在该体验中，警报代表规则和触发的警报，因此可以处于“警告”、“活动”或“已禁用”状态。
    >

## <a name="single-place-to-view-and-manage-alerts"></a>在单个位置查看和管理警报
“警报”体验的目的是在一个位置查看和管理所有 Azure 警报。 以下小节介绍新体验的每个屏幕的功能。

### <a name="alerts-overview-page"></a>“警报概述”页
**监视器 - 警报**概述页显示了所有已触发警报和所有已配置/已启用警报规则的聚合摘要， 同时还显示了所有已触发警报的列表。 更改订阅或筛选器参数可以更新聚合和已触发警报列表。

> [!NOTE]
> “警报”中显示的已触发警报仅限支持的指标警报和活动日志警报；“Azure Monitor 概述”显示已触发警报（包括旧版 Azure 警报中的警报）的计数

 ![警报概述](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>警报规则管理
“监视器 - 警报 > 规则”是单个页面，用于跨 Azure 订阅管理所有警报规则。 此页面列出所有警报规则（已启用的或已禁用的），这些规则可以根据目标资源、资源组、规则名称或状态排序。 也可在此页禁用/启用或编辑警报规则。  

 ![警报规则](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>跨所有监视源的单一警报创作体验
在全新警报体验中，可以通过一致的方式创作警报，而不考虑监视服务或信号类型。 单个页面中提供了所有触发的警报和相关详细信息。  
 
创作警报分为三个步骤：用户首先选取警报的目标，接着选择适当的信号，再指定需要根据警报规则应用于信号的逻辑。 这个创作过程经过了简化，用户在选择 Azure 资源之前，不再需要知道支持的监视源或信号。 常见的创作体验会自动根据所选目标资源筛选可用信号的列表，并引导用户创建警报逻辑。

[此处](monitor-alerts-unified-usage.md)详细介绍了如何创建以下警报类型。
- 指标警报
- 日志警报 (Log Analytics)
- 日志警报（活动日志）
- 日志警报 (Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>新体验中支持的警报
警报可在多个 Azure 监视服务中使用。 有关如何以及何时使用这些服务的信息，请参阅[此文](./monitoring-overview.md)。 此处是细分的警报类型可用在 Azure 和通过新的警报体验当前支持的功能。 


| **信号类型** | **监视器源** | **说明** | 
|-------------|----------------|-------------|
| 指标 | Azure Monitor | 也称为[近实时指标警报](monitoring-near-real-time-metric-alerts.md)，这些指标警报支持以 1 分钟 1 次的频率对指标条件进行评估，并且允许多指标和多维指标规则。 [此处](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)提供支持的资源类型的列表。 |
| 指标 | Azure Monitor | 新警报体验不支持[旧式经典指标警报](monitoring-overview-alerts.md)。 可以在 Azure 门户中的“警报(经典)”下找到这些警报。 经典警报支持某些尚未移到更新警报的指标类型。 有关完整列表，请参阅[支持的指标](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-supported-metrics)
| 日志  | Log Analytics | 当针对指标和/或事件数据进行的日志搜索查询符合特定的条件时，会收到通知（此时也可让系统运行自动化操作）。 旧式 Log Analytics 警报仍可用，但[正在复制到新体验](monitoring-alerts-extend.md)。 此外，可以使用 [*Log Analytics 日志即指标预览版*](monitoring-alerts-extend-tool.md)。 使用预览版可以提取某些类型的日志并将其转换为指标，然后，可以使用新的警报体验对其发出警报。 如果想要连同本机 Azure Monitor 指标一起获取非 Azure 日志，预览版很有用。 |
| 活动日志 | 活动日志（常规） | 包含通过所选目标（资源/资源组/订阅）执行的所有创建、更新和删除操作的记录。 |
| 活动日志  | 服务运行状况 | 在新警报体验中不受支持。 请参阅[创建有关服务通知的活动日志警报](monitoring-activity-log-alerts-on-service-notifications.md)。  |
| 日志  | Application Insights | 包含具有应用程序的性能详细信息的日志。 使用分析查询可以基于应用程序数据定义要执行的操作的条件。 |
| 指标 | Application Insights | 在新警报体验中不受支持。 请参阅[指标警报](../application-insights/app-insights-alerts.md) |
| Web 可用性测试 | Application Insights | 在“警报”体验中不受支持。  请参阅 [Web 测试警报](../application-insights/app-insights-monitor-web-app-availability.md)。 适用于任何经检测可将数据发送到 Application Insights 的网站。 网站的可用性或响应度低于预期时，就会收到通知。 |




## <a name="next-steps"></a>后续步骤
- [了解如何使用全新警报体验创建、查看和管理警报](monitor-alerts-unified-usage.md)
- [了解“警报”体验中的日志警报](monitor-alerts-unified-log.md)
- [了解“警报”体验中的指标警报](monitoring-near-real-time-metric-alerts.md)
- [了解“警报”体验中的活动日志警报](monitoring-activity-log-alerts-new-experience.md)
