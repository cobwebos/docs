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
ms.openlocfilehash: 356988e8ae743d73c8e2cc7cc106cbc5b0d1a423
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Azure Monitor 中的全新警报体验

## <a name="overview"></a>概述
警报具有新体验。 旧版警报体验现在在“警报(经典)”选项卡下提供。全新警报体验与“警报(经典)”体验相比具有以下优点：

 - **分开触发的警报和警报规则** - 对警报规则（定义触发警报的条件）和触发的警报（警报规则触发实例）进行了区分，因此操作视图和配置视图是分开的。
 - **统一的创作体验** - 针对 Azure Monitor、Log Analytics 和 Application Insights 中的指标、日志和活动日志的所有警报创建均在一个位置进行。 
 - **在 Azure 门户中查看触发的 Log Analytics 警报** - 现在还可以在订阅中查看触发的 Log Analytics 警报。 以前这些警报在单独的门户中。 
 - **更好的工作流** - 全新警报创作体验引导用户完成警报规则配置过程，因此发现需要发出警报的事项变得更容易了。
 

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

 ![警报概述](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>警报规则管理
“监视器 - 警报 > 规则”是单个页面，用于跨 Azure 订阅管理所有警报规则。 此页面列出所有警报规则（已启用的或已禁用的），这些规则可以根据目标资源、资源组、规则名称或状态排序。 也可在此页禁用/启用或编辑警报规则。  

 ![警报规则](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>跨所有监视源的单一警报创作体验
在全新警报体验中，可以通过一致的方式创作警报，而不考虑监视服务或信号类型。 单个页面中提供了所有触发的警报和相关详细信息。  
 
创作警报分为三个步骤：用户首先选取警报的目标，接着选择适当的信号，再指定需要根据警报规则应用于信号的逻辑。 这个创作过程经过了简化，用户在选择 Azure 资源之前，不再需要知道支持的监视源或信号。 常见的创作体验会自动根据所选目标资源筛选可用信号的列表，并引导用户创建警报逻辑。

[此处](monitor-alerts-unified-usage.md)详细介绍了如何创建以下警报类型。
- 指标警报
- 日志警报 (Log Analytics)
- 日志警报（活动日志）
- 日志警报 (Application Insights)

 

## <a name="alert-types-supported"></a>支持的警报类型


| **信号类型** | **监视器源** | **说明** | 
|-------------|----------------|-------------|
| 指标 | Azure Monitor | 也称为[**近实时指标警报**](monitoring-near-real-time-metric-alerts.md)，这些指标警报支持以 1 分钟 1 次的频率对指标条件进行评估，并且允许多指标规则。 [此处](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)提供支持的资源类型的列表。 全新警报体验不支持[此处](monitoring-overview-alerts.md#alerts-in-different-azure-services)定义的旧版指标警报。 可以在“警报(经典)”下找到这些警报|
| 日志  | Log Analytics | 当针对指标和/或事件数据进行的日志搜索查询符合特定的条件时，会收到通知（此时也可让系统运行自动化操作）。|
| 活动日志 | 活动日志 | 此类别包含通过所选目标（资源/资源组/订阅）执行的所有创建、更新和删除操作的记录。 |
| 日志  | 服务运行状况日志 | 在“警报”体验中不受支持。   |
| 日志  | Application Insights | 此类别包含具有应用程序的性能详细信息的日志。 使用分析查询可以基于应用程序数据定义要执行的操作的条件。 |
| 指标 | Application Insights | 在“警报”体验中不受支持。 将在“警报(经典)”下找到这些警报 |
| 可用性测试 | Application Insights | 在“警报”体验中不受支持。 将在“警报(经典)”下找到这些警报 |


## <a name="next-steps"></a>后续步骤
- [了解如何使用全新警报体验创建、查看和管理警报](monitor-alerts-unified-usage.md)
- [了解“警报”体验中的日志警报](monitor-alerts-unified-log.md)
- [了解“警报”体验中的指标警报](monitoring-near-real-time-metric-alerts.md)
- [了解“警报”体验中的活动日志警报](monitoring-activity-log-alerts-new-experience.md)
