---
title: 了解指标警报在 Azure Monitor 中的工作原理。
description: 获取指标警报功能的概述，以及它们在 Azure Monitor 中的工作原理。
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 1ec47ddf5769dd8ed624277a86db57f449581b90
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948683"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>了解指标警报在 Azure Monitor 中的工作原理

Azure Monitor 中的指标警报建立在多维指标的基础之上。 这些指标可能是平台指标、自定义指标（预览版）、Log Analytics 中已转换为指标的常用日志，以及 Application Insights 标准指标。 指标警报定期评估，以检查某个指标时序的条件是否属实，并在符合评估条件时发出通知。 指标警报是有状态的，即，它们只会在状态有更改时才发出通知。

## <a name="how-do-metric-alerts-work"></a>指标警报的工作原理

可以通过指定要监视的目标资源、指标名称和条件（运算符与阈值），以及警报规则激发时要触发的操作组，来定义指标警报规则。
假设你创建了一个如下所述的简单指标警报规则：

- 目标资源（要监视的 Azure 资源）：myVM
- 指标：CPU 百分比
- 时间聚合（基于原始指标值运行的统计信息。 支持的时间聚合为“最小值”、“最大值”、“平均值”、“总计”）：平均值
- 期限（检查指标值时所依据的回溯时段）：过去 5 分钟
- 频率（指标警报检查是否符合条件的频率）：1 分钟
- 运算符：大于
- 阈值：70

从创建警报规则的时间开始，监视器将每隔 1 分钟运行，查看过去 5 分钟的指标值，并检查这些值的平均值是否超过 70。 如果符合条件（即，过去 5 分钟的平均 CPU 百分比超过 70），则警报规则将激发激活的通知。 如果在与警报规则关联的操作组中配置了电子邮件或 Webhook，则两者都会收到激活的通知。

也可以在 Azure 门户的“所有警报”边栏选项卡中查看此特定的警报规则激发实例。

假设在后续的检查中，“myVM”上的用量持续超过阈值，则在解决这种状况之前，警报规则不会再次激发。

一段时间后，如果“myVM”上的用量回归正常，即低于指定的阈值， 则警报规则将再监视条件两次，然后发出“已解决”通知。 如果在三个连续的期限内都不符合警报条件，则警报规则会发出“已解决”/“已停用”消息，以便在不稳定的环境中减少干扰。

通过 Webhook 或电子邮件发出“已解决”通知后，Azure 门户中警报实例的状态（称为“监视状态”）也会设置为“已解决”。

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>使用 Azure Monitor 中的指标警报进行大规模监视

### <a name="using-dimensions"></a>使用维度

Azure Monitor 中的指标警报还支持使用一个规则来监视多个维度值的组合。 下面举例说明为何使用多维组合会有所帮助。

假设你为网站创建了一个应用服务计划。 你想要监视运行网站/应用的多个实例上的 CPU 使用率。 可以使用如下所述的指标警报规则实现此目的

- 目标资源：myAppServicePlan
- 指标：CPU 百分比
- 维度
  - 实例 = InstanceName1、InstanceName2
- 时间聚合：平均值
- 期限：过去 5 分钟
- 频率：1 分钟
- 运算符：大于
- 阈值：70

如前所述，此规则会监视过去 5 分钟的平均 CPU 使用率是否超过 70%。 但是，使用相同的规则可以监视运行网站的两个实例。 每个实例单独受到监视，而你会分别收到不同的通知。

假设你的 Web 应用需要应对很高的需求，因此你需要添加更多的实例。 上述规则依然只会监视两个实例。 但是，可以创建如下所述的规则。

- 目标资源：myAppServicePlan
- 指标：CPU 百分比
- 维度
  - 实例 = *
- 时间聚合：平均值
- 期限：过去 5 分钟
- 频率：1 分钟
- 运算符：大于
- 阈值：70

此规则将自动监视实例的所有值，即， 可以在实例联机时对其进行监视，而无需再次修改指标警报规则。

### <a name="monitoring-multiple-resource-using-metric-alerts"></a>使用指标警报监视多个资源

如前一部分中所述，可以使用单个指标警报规则来监视每个维度组合（即 指标时序）。 但是，仍受限于每次对一个资源进行监视。 指标警报预览版现在还支持使用一个规则监视多个资源。 如果订阅中有数百个 VM，此新功能可以帮助快速设置这些 VM 的监视。 

此功能目前处于预览状态。 目前，不支持通过 Azure 门户创建用于监视多个资源的指标警报规则。 可以通过 Azure 资源管理器模板创建这些规则。

## <a name="typical-latency"></a>典型延迟

对于指标警报，如果将警报规则频率设置为 1 分钟，则通常会在 5 分钟以内收到通知。如果通知系统承受很高的负载，则延迟时间可能更长。

## <a name="supported-resource-types-for-metric-alerts"></a>指标警报支持的资源类型

可在[此文](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)中找到受支持资源类型的完整列表

## <a name="next-steps"></a>后续步骤

- [了解如何在 Azure 中创建、查看和管理指标警报](alert-metric.md)
- [了解如何使用 Azure 资源管理器模板部署指标警报](monitoring-create-metric-alerts-with-templates.md)
- [详细了解操作组](monitoring-action-groups.md)
