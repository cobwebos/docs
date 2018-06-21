---
title: 将 Log Analytics 警报扩展（复制）到 Azure 警报中 - 概述
description: 概述了将警报从 OMS 门户中的 Log Analytics 复制到 Azure 警报的过程，并详细探讨了常见客户问题。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: c01f986bfe3e6a3162988056a268423e07f429bf
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301144"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>将 Log Analytics 警报扩展到 Azure 警报
Azure Log Analytics 包含自身的警报功能，可以根据 Log Analytics 数据将相关情况主动通知给你，这种情况到最近才有所改变。 你过去在 [Microsoft Operations Management Suite 门户](../operations-management-suite/operations-management-suite-overview.md)中管理警报规则。 新的警报体验现在已在 Microsoft Azure 的各种服务中集成了警报。 该体验在 Azure 门户中通过 Azure Monitor 下的“警报”提供，支持的警报来自活动日志、指标以及 Log Analytics 和 Azure Application Insights 提供的日志。 

## <a name="benefits-of-extending-your-alerts"></a>对警报进行扩展的好处
在 Azure 门户中创建和管理警报有多种优势，例如：

- 在 Operations Management Suite 门户中，只能创建和查看 250 个警报，与之不同，在 Azure 警报中不存在此限制。
- 在 Azure 警报中，可管理、枚举和查看所有警报类型。 在以前，只能对 Log Analytics 警报这样做。
- 可以使用 [Azure Monitor 角色](monitoring-roles-permissions-security.md)将用户的访问权限限制为仅监视和警报。
- 在 Azure 警报中，可以使用[操作组](monitoring-action-groups.md)。 这样就可以为每个警报设置多个操作。 可以发送短信、发送语音呼叫、调用 Azure 自动化 Runbook、调用 Webhook、配置 IT 服务管理 (ITSM) 连接器。 

## <a name="process-of-extending-your-alerts"></a>对警报进行扩展的流程
将警报从 Log Analytics 移到 Azure 警报中这一过程不涉及以任何方式更改警报定义、查询或配置。 唯一需要进行的更改是，在 Azure 中，你使用操作组执行所有操作。 如果操作组已与警报相关联，则在扩展到 Azure 中时，操作组也会包括进去。

> [!NOTE]
> 从 2018 年 5 月 14 日开始，Microsoft 会将 Log Analytics 中创建的警报自动扩展到 Azure 警报，此过程会一直重复到完成为止。 如果在创建[操作组](monitoring-action-groups.md)的过程中出现任何问题，请执行[这些修正步骤](monitoring-alerts-extend-tool.md#troubleshooting)，以便自动创建操作组。 在 2018 年 7 月 5 日之前，一直可以使用这些步骤。 
> 

在计划将 Log Analytics 工作区中的警报扩展到 Azure 中时，这些警报可以一直使用，不会以任何方式影响你的配置。 在计划以后，警报可能暂时不可修改；但在此期间，可以继续创建新的 Azure 警报。 如果尝试在 Operations Management Suite 门户中编辑或创建警报，可以选择在 Log Analytics 工作区中继续创建它们。 也可选择在 Azure 门户的 Azure 警报中创建它们。

 ![在 Log Analytics 或 Azure 警报中创建警报时的选项的屏幕截图](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> 将警报从 Log Analytics 扩展到 Azure 不会对帐户收费。 当在 [Azure Monitor 定价策略](https://azure.microsoft.com/pricing/details/monitor/)中声明的限制和条件范围内使用时，将 Azure 警报用于基于查询的 Log Analytics 警报不会计费。  


### <a name="how-to-extend-your-alerts-voluntarily"></a>如何主动扩展警报
若要将警报扩展到 Azure 警报，可以使用 Operations Management Suite 门户中提供的向导，也可以通过 API 以编程方式这样做。 有关详细信息，请参阅[使用 Operations Management Suite 门户和 API 将警报扩展到 Azure](monitoring-alerts-extend-tool.md)。

## <a name="experience-after-extending-your-alerts"></a>扩展警报后的体验
将警报扩展到 Azure 警报以后，可以继续在 Operations Management Suite 门户中对其进行管理，与之前无异。

![Operations Management Suite 门户的屏幕截图，其中列出了警报](./media/monitor-alerts-extend/PostExtendList.png)

在 Operations Management Suite 门户中尝试编辑现有警报或创建新警报时，会自动重定向到 Azure 警报。  

> [!NOTE]
> 确保在 Azure 中正确分配需要添加或编辑警报的个人用户的权限。 若要了解需授予的具体权限，请参阅[使用 Azure Monitor 和警报的权限](monitoring-roles-permissions-security.md)。  
> 

可以继续通过 [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) 和 [Log Analytics 资源模板](../monitoring/monitoring-solutions-resources-searches-alerts.md)创建警报。 执行此操作时，必须包括操作组。

## <a name="next-steps"></a>后续步骤

* 了解[启动将警报从 Log Analytics 扩展到 Azure 的流程](monitoring-alerts-extend-tool.md)时使用的工具。
* 详细了解 [Azure 警报体验](monitoring-overview-unified-alerts.md)。
* 了解然后创建 [Azure 警报中的日志警报](monitor-alerts-unified-log.md)。
