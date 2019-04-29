---
title: 了解指标警报在 Azure Monitor 中的工作原理。
description: 获取指标警报功能的概述，以及它们在 Azure Monitor 中的工作原理。
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 59973d9530bf1c3ab3e77290b25e50860f9de0ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712859"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>了解指标警报在 Azure Monitor 中的工作原理

Azure Monitor 中的指标警报建立在多维指标的基础之上。 这些指标可能是[平台指标](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)、[自定义指标](../../azure-monitor/platform/metrics-custom-overview.md)、[Azure Monitor 中已转换为指标的常用日志](../../azure-monitor/platform/alerts-metric-logs.md)，以及 Application Insights 指标。 指标警报定期评估，以检查一个或多个指标时序的条件是否属实，并在符合评估条件时发出通知。 指标警报是有状态的，即，它们只会在状态有更改时才发出通知。

## <a name="how-do-metric-alerts-work"></a>指标警报的工作原理

可以通过指定要监视的目标资源、指标名称、条件类型（静态或动态）和条件（运算符和阈值/敏感度），以及警报规则激发时要触发的操作组，来定义指标警报规则。 条件类型影响阈值的确定方式。 [详细了解动态阈值条件类型和敏感度选项](alerts-dynamic-thresholds.md)。

### <a name="alert-rule-with-static-condition-type"></a>使用静态条件类型的警报规则

假设你创建了一个如下所述的简单静态阈值指标警报规则：

- 目标资源（要监视的 Azure 资源）：myVM
- 指标：CPU 百分比
- 条件类型：静态
- 时间聚合（基于原始指标值运行的统计信息。 支持的时间聚合为“最小值”、“最大值”、“平均值”、“总计”）：平均值
- 期限（检查指标值时所依据的回溯时段）：过去 5 分钟
- 频率（指标警报检查是否符合条件的频率）：1 分钟
- 运算符：大于
- 阈值：70

从创建警报规则的时间开始，监视器将每隔 1 分钟运行，查看过去 5 分钟的指标值，并检查这些值的平均值是否超过 70。 如果符合条件（即，过去 5 分钟的平均 CPU 百分比超过 70），则警报规则将激发激活的通知。 如果在与警报规则关联的操作组中配置了电子邮件或 Webhook，则两者都会收到激活的通知。

### <a name="alert-rule-with-dynamic-condition-type"></a>使用动态条件类型的警报规则

假设你创建了一个如下所述的简单动态阈值指标警报规则：

- 目标资源（要监视的 Azure 资源）：myVM
- 指标：CPU 百分比
- 条件类型：动态
- 时间聚合（基于原始指标值运行的统计信息。 支持的时间聚合为“最小值”、“最大值”、“平均值”、“总计”）：平均值
- 期限（检查指标值时所依据的回溯时段）：过去 5 分钟
- 频率（指标警报检查是否符合条件的频率）：1 分钟
- 运算符：大于
- 敏感度：中型
- 回溯时段：4
- 违规次数：4

创建警报规则后，动态阈值机器学习算法将获取可用的历史数据，计算最适合指标系列行为模式的阈值，并基于新数据持续学习，使阈值更准确。

从创建警报规则时开始算起，监视器将每隔 1 分钟运行，查看过去 20 分钟内每 5 分钟时段的指标值，并检查每个时段（共 4 个时段）的平均值是否超过预期阈值。 如果符合条件，即，过去 20 分钟（有 4 个 5 分钟时段）的平均 CPU 百分比与预期行为相差 4 倍，则警报规则将激发激活的通知。 如果在与警报规则关联的操作组中配置了电子邮件或 Webhook，则两者都会收到激活的通知。

### <a name="view-and-resolution-of-fired-alerts"></a>查看并解决已激发的警报

也可以在 Azure 门户的“所有警报”边栏选项卡中查看上述警报规则激发示例。

假设在后续的检查中，“myVM”上的用量持续超过阈值，则在解决这种状况之前，警报规则不会再次激发。

一段时间后，如果“myVM”上的用量回归正常，即低于阈值， 则警报规则将再监视条件两次，然后发出“已解决”通知。 如果在三个连续的期限内都不符合警报条件，则警报规则会发出“已解决”/“已停用”消息，以便在不稳定的环境中减少干扰。

通过 Webhook 或电子邮件发出“已解决”通知后，Azure 门户中警报实例的状态（称为“监视状态”）也会设置为“已解决”。

### <a name="using-dimensions"></a>使用维度

Azure Monitor 中的指标警报还支持使用一个规则来监视多个维度值的组合。 下面举例说明为何使用多维组合会有所帮助。

假设你为网站创建了一个应用服务计划。 你想要监视运行网站/应用的多个实例上的 CPU 使用率。 可以使用如下所述的指标警报规则实现此目的：

- 目标资源：myAppServicePlan
- 指标：CPU 百分比
- 条件类型：静态
- 维度
  - 实例 = InstanceName1、InstanceName2
- 时间聚合：平均值
- 期限：过去 5 分钟
- 频率：1 分钟
- 运算符：GreaterThan
- 阈值：70

如前所述，此规则会监视过去 5 分钟的平均 CPU 使用率是否超过 70%。 但是，使用相同的规则可以监视运行网站的两个实例。 每个实例单独受到监视，而你会分别收到不同的通知。

假设你的 Web 应用需要应对很高的需求，因此你需要添加更多的实例。 上述规则依然只会监视两个实例。 但是，可以创建如下所述的规则：

- 目标资源：myAppServicePlan
- 指标：CPU 百分比
- 条件类型：静态
- 维度
  - 实例 = *
- 时间聚合：平均值
- 期限：过去 5 分钟
- 频率：1 分钟
- 运算符：GreaterThan
- 阈值：70

此规则将自动监视实例的所有值，即， 可以在实例联机时对其进行监视，而无需再次修改指标警报规则。

监视多个维度时，动态阈值警报规则一次可为数百个指标系列创建定制的阈值。 动态阈值可以减少要管理的警报规则数目，并可以大幅节省警报规则的管理和创建时间。

假设你的 Web 应用包含许多实例，而你不知道什么最合适的阈值是什么。 上述规则始终使用阈值 70%。 但是，可以创建如下所述的规则：

- 目标资源：myAppServicePlan
- 指标：CPU 百分比
- 条件类型：动态
- 维度
  - 实例 = *
- 时间聚合：平均值
- 期限：过去 5 分钟
- 频率：1 分钟
- 运算符：GreaterThan
- 敏感度：中型
- 回溯时段：第
- 违规次数：第

此规则会监视过去 5 分钟的平均 CPU 使用率是否超过每个实例的预期行为。 同一规则可以在实例联机时对其进行监视，而无需再次修改指标警报规则。 每个实例将获得一个符合指标系列行为模式的阈值，并基于新数据持续进行更改，使阈值更准确。 如前所述，每个实例单独受到监视，而你会分别收到不同的通知。

增加回溯时段和违规次数还可以将警报筛选为针对重大偏差定义的警报。 [详细了解动态阈值高级选项](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean)。

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>使用 Azure Monitor 中的指标警报进行大规模监视

到目前为止，已了解了如何使用单个指标警报监视与单个 Azure 资源相关的一个或多个指标时序。 很多时候，你可能希望将同一预警规则应用于许多资源。 Azure Monitor 还支持使用一个指标警报规则监视多个资源。 目前仅虚拟机支持此功能。 此外，单个指标警报可以监视一个 Azure 区域中的资源。

可以通过以下三种方式之一指定单个指标警报的监视范围：

- 指定为单个订阅中单个 Azure 区域中的虚拟机列表
- 指定为单个订阅中一个或多个资源组中的所有虚拟机（在单个 Azure 区域中）
- 指定为单个订阅中的所有虚拟机（在单个 Azure 区域中）

创建监视多个资源的指标预警规则类似于[创建监视单个资源的任何其他指标警报](alerts-metric.md)。 唯一区别是，你将选择要监视的所有资源。 也可以通过 [Azure 资源管理器模板](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources)创建这些规则。 对于每台虚拟机，你将收到单独的通知。

## <a name="typical-latency"></a>典型延迟

对于指标警报，如果将警报规则频率设置为 1 分钟，则通常会在 5 分钟以内收到通知。如果通知系统承受很高的负载，则延迟时间可能更长。

## <a name="supported-resource-types-for-metric-alerts"></a>指标警报支持的资源类型

可在[此文](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)中找到受支持资源类型的完整列表。

如果你现在使用经典指标警报并且想要了解指标警报是否支持所使用的所有资源类型，则下表显示了经典指标警报支持的资源类型，以及这些资源类型现在是否受到指标警报支持。

|经典指标警报支持的资源类型 | 受指标警报支持 |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | 是 |
| Microsoft.Batch/batchAccounts| 是|
|Microsoft.Cache/redis| 是 |
|Microsoft.ClassicCompute/virtualMachines | 否 |
|Microsoft.ClassicCompute/domainNames/slots/roles | 否|
|Microsoft.CognitiveServices/accounts | 否 |
|Microsoft.Compute/virtualMachines | 是|
|Microsoft.Compute/virtualMachineScaleSets| 是|
|Microsoft.ClassicStorage/storageAccounts| 否 |
|Microsoft.DataFactory/datafactories | 是|
|Microsoft.DBforMySQL/servers| 是|
|Microsoft.DBforPostgreSQL/servers| 是|
|Microsoft.Devices/IotHubs | 否|
|Microsoft.DocumentDB/databaseAccounts| 是|
|Microsoft.EventHub/namespaces | 是|
|Microsoft.Logic/workflows | 是|
|Microsoft.Network/loadBalancers |是|
|Microsoft.Network/publicIPAddresses| 是|
|Microsoft.Network/applicationGateways| 是|
|Microsoft.Network/expressRouteCircuits| 是|
|Microsoft.Network/trafficManagerProfiles | 是|
|Microsoft.Search/searchServices | 是|
|Microsoft.ServiceBus/namespaces| 是 |
|Microsoft.Storage/storageAccounts | 是|
|Microsoft.StreamAnalytics/streamingjobs| 是|
|Microsoft.TimeSeriesInsights/environments | 是|
|Microsoft. Web/serverfarms | 是 |
|Microsoft. Web/sites（不包括函数） | 是|
|Microsoft. Web/hostingEnvironments/multiRolePools | 否|
|Microsoft. Web/hostingEnvironments/workerPools| 否 |
|Microsoft.SQL/Servers | 否 |

## <a name="next-steps"></a>后续步骤

- [了解如何在 Azure 中创建、查看和管理指标警报](alerts-metric.md)
- [了解如何使用 Azure 资源管理器模板部署指标警报](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [详细了解操作组](action-groups.md)
- [详细了解动态阈值条件类型](alerts-dynamic-thresholds.md)
