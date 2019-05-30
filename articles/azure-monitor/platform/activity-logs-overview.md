---
title: Azure 活动日志概述
description: 了解什么是 Azure 活动日志，以及如何通过它了解发生在 Azure 订阅中的事件。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 34857108cf7f0580c380ffbd4bbcedb5cd5a807a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245025"
---
# <a name="overview-of-azure-activity-log"></a>Azure 活动日志概述

**Azure 活动日志**深入了解 Azure 中发生的订阅级别事件。 这包括从 Azure 资源管理器操作数据到服务运行状况事件更新的一系列数据。 活动日志以前称为_审核日志_或_操作日志_，因为管理类别报告订阅的控制平面事件。 

使用活动日志，以确定_什么_，_谁_，并_时_的任何写入操作 (PUT、 POST、 DELETE) 在订阅中的资源执行的。 还可以了解该操作和其他相关属性的状态。 

活动日志不包括读取 (GET) 操作或使用经典 /RDFE 模型的资源的操作。

## <a name="comparison-to-diagnostic-logs"></a>与诊断日志之间的比较
没有为每个 Azure 订阅单个活动日志。 它提供了有关从外部资源的操作数据 （"控制面"）。 [诊断日志](diagnostic-logs-overview.md)由资源发出，并提供有关该资源 （"数据面"） 的操作的信息。 必须启用每个资源的诊断设置。

![相比诊断日志的活动日志](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure 活动日志主要用于在 Azure 资源管理器中发生的活动。 它不跟踪 ugresources 使用经典 /RDFE 模型。 某些经典资源类型在 Azure 资源管理器中具有代理资源提供程序（例如 Microsoft.ClassicCompute）。 如果通过 Azure 资源管理器使用这些代理资源提供程序与经典资源类型进行交互，则操作会显示在活动日志中。 如果在 Azure 资源管理器代理外部与经典资源类型进行交互，则操作只会记录在操作日志中。 可以在门户的一个单独部分中浏览操作日志。

## <a name="activity-log-retention"></a>活动日志保留期
活动日志事件存储 90 天。 若要将此数据存储，以便延长[收集在 Azure Monitor](activity-log-collect.md)或[将其导出到存储或事件中心](activity-log-export.md)。

## <a name="view-the-activity-log"></a>查看活动日志
查看所有资源的活动日志**监视器**在 Azure 门户中的菜单。 查看从特定资源的活动日志**活动日志**该资源的菜单中的选项。 此外可以检索与 PowerShell、 CLI 或 REST API 的活动日志记录。  请参阅[视图和检索 Azure 活动日志事件](activity-log-view.md)。

![查看活动日志](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>收集 Azure Monitor 中的活动日志
将活动日志收集到 Azure Monitor 来分析与其他监视数据，并保留的时间超过 90 天的数据中的 Log Analytics 工作区。 请参阅[收集和分析 Azure Monitor 中的 Log Analytics 工作区中的 Azure 活动日志](activity-log-collect.md)。

![查询活动日志](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>导出活动日志
将活动日志导出到 Azure 存储用于存档或第三方服务或自定义分析解决方案流式传输到事件中心引入的。 请参阅[Azure 活动日志导出](activity-log-export.md)。 你也可以分析在 Power BI 中使用活动日志事件[ **Power BI 内容包**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)。

## <a name="alert-on-activity-log"></a>针对活动日志警报
在活动日志中创建特定事件时，可以创建一个警报[活动日志警报](activity-log-alerts.md)。 此外可以创建警报使用[日志查询](alerts-log-query.md)时活动日志连接到 Log Analytics 工作区中，但没有成本来记录查询警报。 不没有用于活动日志警报的任何费用。

## <a name="categories-in-the-activity-log"></a>活动日志中的类别
活动日志中的每个事件都有特定的类别下表中所述。 有关这些类别的架构的完整详细信息，请参阅 [Azure 活动日志事件架构](activity-log-schema.md)。 

| 类别 | 描述 |
|:---|:---|
| 管理 | 包含记录所有创建、 更新、 删除和操作的操作通过资源管理器中执行。 管理事件的示例包括_创建虚拟机_并_删除网络安全组_。<br><br>每个用户或应用程序使用资源管理器所执行的操作都建模为特定资源类型上的操作。 如果操作类型为_编写_，_删除_，或_操作_，在管理类别中记录的记录的开始、 成功或失败该操作。 管理事件还包括对基于角色的访问控制的任何更改在订阅中。 |
| 服务运行状况 | 包含 Azure 中发生任何服务运行状况事件的记录。 服务运行状况事件的一个示例_美国东部的 SQL Azure 正处于故障时间_。 <br><br>服务运行状况事件分 5 种：_所需的操作_，_辅助恢复_，_事件_，_维护_，_信息_，或_安全_。 如果您在将受事件影响的订阅中的资源，仅创建这些事件。
| 资源运行状况 | 包含对 Azure 资源发生的任何资源运行状况事件的记录。 资源运行状况事件的一个示例是_虚拟机运行状况状态更改为不可用_。<br><br>资源运行状况事件可以表示四个运行状况状态之一：_可用_，_不可用_，_降级_，并且_未知_。 此外，资源运行状况事件可以划分为正在_平台启动_或_用户启动的_。 |
| 警报 | 包含的 Azure 警报激活的记录。 警报事件的一个示例是_myVM 上的 CPU 百分比已超过 80 过去 5 分钟_。|
| 自动缩放 | 包含与基于你的订阅中定义的任何自动缩放设置的自动缩放引擎操作相关的任何事件的记录。 自动缩放事件的一个示例是_自动缩放扩展操作失败_。 |
| 建议 | 包含 Azure 顾问的建议事件。 |
| 安全 | 包含 Azure 安全中心生成的任何警报的记录。 安全事件的一个示例是_执行的可疑的双扩展名文件_。 |
| 策略 | 包含所有效果操作执行的操作的 Azure 策略的记录。 策略事件的示例包括_审核_并_拒绝_。 Policy 执行的每个操作建模为对资源执行的操作。 |


## <a name="next-steps"></a>后续步骤

* [创建日志配置文件导出 Azure 活动日志](activity-log-export.md)
* [将 Azure 活动日志流式传输到事件中心](activity-logs-stream-event-hubs.md)
* [Azure 活动日志存档到存储](archive-activity-log.md)

