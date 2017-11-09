---
title: "Azure Monitor 中的准实时指标警报 | Microsoft 文档"
description: "通过准实时指标警报，你可以 1 分钟一次的频率监视 Azure 资源指标。"
author: snehithm
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
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>准实时指标警报（预览版）
Azure Monitor 现在支持一种称为准实时指标警报（预览版）的新型指标警报。 此功能目前处于公开预览状态。
这些警报与常规指标警报有一些不同

- 改进了延迟 - 准实时指标警报可以在 1 分钟内监控指标值的变化。
- 更好地控制指标条件 - 准实时指标警报使用户能够定义更丰富的警报规则。 警报现在支持监视指标的最大值、最小值、平均值和总值。
- 综合监控多个指标 - 准实时指标警报可以使用单个规则监控多个指标（目前为两个）。 如果两个指标在指定时间段内违反其各自的阈值，则会触发警报。
- 模块化通知系统 - 准实时指标警报使用[操作组](monitoring-action-groups.md)。 此功能允许用户以模块化方式创建操作，并将其重复用于诸多警报规则。

> [!NOTE]
> 准实时指标警报功能目前处于公开预览状态。 功能和用户体验可能会发生变化。
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>可以为哪些资源创建准实时指标警报？
准实时指标警报支持的完整资源类型列表：

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>创建准实时指标警报
目前，准实时指标警报只能通过 Azure 门户创建。 即将推出通过 PowerShell、命令行接口 (CLI) 和 Azure Monitor REST API 配置准实时指标警报的支持功能。

1. 在此[门户](https://portal.azure.com/)，查找想要监视的资源并选中它。 该资源应该是[上一节](#what-resources-can-i-create-near-real-time-metric-alerts-for)中列出的资源类型之一。 此外，你也可以从“监视”>“警报”集中对所有支持的资源类型执行相同操作。

2. 在“监视”部分下，选择“警报”或“警报规则”。 对于不同的资源，文本和图标可能会略有不同。
   ![监视](./media/insights-alerts-portal/AlertRulesButton.png)

3. 单击“添加准实时指标警报(预览)”命令。 如果该命令灰显，请确保在筛选器中选中资源。

    ![“添加准实时指标警报”按钮](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. **命名**警报规则，并选择也在通知电子邮件中显示的“说明”。
5. 选择想要监视的“指标”，然后为该指标依次选择“条件”、“时间聚合”和“阈值”。 也可以选择想要监视的另一个“指标”，然后为其依次选择“条件”、“时间聚合”和“阈值”。 

    ![添加准实时指标警报1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) ![添加准实时指标警报2](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. 选择触发警报前指标规则必须满足的时间段。 例如，如果使用时间段“过去 5 分钟”，警报针对 CPU 使用率高于 80%（和 NetworkIn 高于 500 MB）的情况，则在 CPU 使用率持续高于 80% 达到 5 分钟时触发警报。 第一次触发后，CPU 5 分钟内持续低于 80% 时会再次触发警报。 警报根据“评估频率”进行评估


6. 从下拉列表中选择适当的“严重性”。

7. 指定是否要使用新建或现有“操作组”。

8. 如果选择创建新操作组，请指定操作组的名称和短名称，指定操作（SMS、Email、Webhook）并填写相应的详细信息。


8. 警报创建完成后，选择“确定”。   

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="managing-near-real-time-metric-alerts"></a>管理准实时指标警报
创建警报后，可选择它并：

* 查看显示指标的阈值和前一天实际值的关系图。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。



