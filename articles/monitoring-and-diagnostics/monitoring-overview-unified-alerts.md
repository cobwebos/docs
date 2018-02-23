---
title: "了解 Azure Monitor 中的全新警报（预览）体验 | Microsoft Docs"
description: "了解如何利用 Azure 中这种全新的既简单又可缩放的警报体验，简化警报的创作、查看和管理"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 316dcd53509897a6efc387749ca6f9ec268cb7ac
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>了解 Azure Monitor 中的全新警报（预览）体验

## <a name="overview"></a>概述
 Azure 中的警报体验有了改观，功能进行了更新。 这种全新体验可以通过 Azure Monitor 下的“警报(预览)”选项卡获取。 下面是使用全新警报（预览）体验的部分优点：

 - **分开触发的警报和警报规则** - 在警报（预览）体验中，对警报规则（定义触发警报的条件）和触发的警报（警报规则触发实例）进行了区分，因此操作视图和配置视图是分开的。
 - **统一了指标警报和日志警报的创作体验** - 全新警报（预览）创作体验引导用户完成警报规则配置过程，因此发现需要发出警报的事项变得更容易了。
 - **在 Azure 门户中查看触发的 Log Analytics 警报** - 在警报（预览）体验中，现在还可以在订阅中查看触发的 Log Analytics 警报。  
 - **活动日志警报的统一创作体验** - 现在可以直接从“监视器” > “警报(预览版)”创建活动日志警报。 也可以只通过“监视器” > “活动日志”创建活动日志警报。

以下部分更详细地介绍了新体验的工作原理。

## <a name="taxonomy"></a>分类
在跨不同的警报类型统一创作体验时，警报（预览）体验使用以下概念来区分警报规则和触发的警报对象。

- **目标资源** - 目标可以是任何 Azure 资源。 目标资源定义适用于警报的范围和信号。 示例目标：虚拟机、存储帐户、虚拟机规模集、Log Analytics 工作区或解决方案。

- **条件** - 条件是应用于目标资源的信号和逻辑的组合。 示例：百分比 CPU > 70%、服务器响应时间 > 4 毫秒、日志查询的结果计数 > 100，等等。 

- **信号** - 信号由目标资源发出，可以有多种类型。 此预览版支持指标、活动日志、Application Insights 和日志作为信号类型。

- **逻辑** - 用户定义逻辑，用于检查信号是否在预期的范围/值内。  
 
- **操作** - 发送给通知接收方的特定通话（例如，通过电子邮件发送地址或发布到 Webhook URL）。 通知通常可以触发多个操作。 此预览版支持的警报类型支持操作组。  
 
- **警报规则** - 定义触发警报的条件。 在此预览版中，警报规则捕获警报的目标和条件。 警报规则可以是“已启用”或“已禁用”状态。
 
- **触发的警报** - 在触发已启用的警报规则时创建。 触发的警报对象可能处于“已触发”或“未解决”状态。

    > [!NOTE]
    > 这不同于当前的警报体验。在当前的体验中，警报代表规则和触发的警报，因此可能处于“警告”、“活动”或“已禁用”状态。
    >

## <a name="single-place-to-view-and-manage-alerts"></a>在单个位置查看和管理警报
警报（预览版）体验的目的是在单个位置查看和管理所有 Azure 警报。 以下小节介绍新体验的每个屏幕的功能。

### <a name="alerts-preview-overview-page"></a>警报（预览）概览页
“监视器 - 警报(预览)”概览页显示了所有已触发警报和所有已配置/已启用警报规则的聚合摘要， 同时还显示了所有已触发警报的列表。 更改订阅或筛选器参数可以更新聚合和已触发警报列表。

> [!NOTE]
> 在警报（预览）中显示的已触发警报仅限支持的指标警报和日志警报；Azure Monitor 显示已触发警报（包括旧版 Azure 警报中的警报）的计数

 ![alerts-preview-overview](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>警报规则管理
“监视器 - 警报(预览) > 规则”是单个页面，用于跨 Azure 订阅管理所有警报规则。 此页面列出所有警报规则（已启用的或已禁用的），这些规则可以根据目标资源、资源组、规则名称或状态排序。 也可在此页禁用/启用或编辑警报规则。  

 ![alerts-preview-rules](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>跨所有监视源的单一警报创作体验
在警报（预览）体验中，可以通过一致的方式创作警报，不管监视服务或信号类型是什么。 单个页面中提供了所有触发的警报和相关详细信息。  
 
创作警报分为三个步骤：用户首先选取警报的目标，接着选择适当的信号，再指定需要根据警报规则应用于信号的逻辑。 这个创作过程经过了简化，用户在选择 Azure 资源之前，不再需要知道支持的监视源或信号。 常见的创作体验会自动根据所选目标资源筛选可用信号的列表，并引导用户创建警报逻辑。

[此处](monitor-alerts-unified-usage.md)详细介绍了如何创建以下警报类型。
- 指标警报（在当前的体验中称为“近实时指标警报”）
- 日志警报 (Log Analytics)
- 日志警报（活动日志）
- 日志警报（Application Insights）

 

## <a name="alert-types-supported-in-this-preview"></a>此预览版中支持的警报类型


| **信号类型** | **监视器源** | **说明** | 
|-------------|----------------|-------------|
| 指标 | Azure Monitor | 在当前的体验中称为[**近实时指标警报**](monitoring-near-real-time-metric-alerts.md)。这些指标警报支持对指标条件进行评估，最高评估频率为 1 分钟 1 次，并且允许多指标规则。 [此处](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for)提供支持的资源类型的列表。 警报（预览）体验不支持[此处](monitoring-overview-alerts.md#alerts-in-different-azure-services)定义的其他指标警报。|
| 日志  | Log Analytics | 当针对指标和/或事件数据进行的日志搜索查询符合特定的条件时，会收到通知（此时也可让系统运行自动化操作）。|
| 日志  | 活动日志 | 此类别包含通过所选目标（资源/资源组/订阅）执行的所有创建、更新和删除操作的记录。 |
| 日志  | 服务运行状况日志 | 在警报（预览）体验中不受支持   |
| 日志  | Application Insights | 此类别包含具有应用程序的性能详细信息的日志。 使用分析查询可以基于应用程序数据定义要执行的操作的条件。 |
| 指标 | Application Insights | 在警报（预览）体验中不受支持 |
| 可用性测试 | Application Insights | 在警报（预览）体验中不受支持 |


## <a name="next-steps"></a>后续步骤
- [了解如何使用全新警报（预览）体验创建、查看和管理警报](monitor-alerts-unified-usage.md)
- [了解警报（预览）体验中的日志警报](monitor-alerts-unified-log.md)
- [了解警报（预览）体验中的指标警报](monitoring-near-real-time-metric-alerts.md)
- [了解警报（预览版）体验中的活动日志警报](monitoring-activity-log-alerts-new-experience.md)
