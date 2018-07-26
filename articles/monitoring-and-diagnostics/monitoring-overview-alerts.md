---
title: Microsoft Azure 和 Azure Monitor 中的经典警报概述
description: 使用警报可以监视 Azure 资源指标、事件或日志，并在符合指定的条件时接收通知。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114005"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Microsoft Azure 中的经典警报是什么？

> [!NOTE]
> 本文介绍了如何创建旧式经典指标警报。 Azure Monitor 现支持[较新的准实时指标警报和新的警报体验](monitoring-overview-unified-alerts.md)。 
>

通过警报，可配置数据条件，并在条件与最新监视数据匹配时收到通知。


## <a name="alerts-on-azure-monitor-data"></a>关于 Azure Monitor 数据的警报
有两种可用的经典警报类型：指标警报和活动日志警报。

* **经典指标警报**：当指定的指标值越过了分配的阈值时，就会触发此警报。 警报在“激活”时（越过阈值并满足警报条件时）生成通知。 它还会在“已解决”时生成警报（再次越过阈值且不再满足条件时）。 

* **经典活动日志警报**：当生成与已分配筛选器条件匹配的活动日志事件时，就会触发此流式处理日志警报。 这些警报只有“已激活”这一个状态，因为警报引擎只将筛选器条件应用到任何新事件。 出现新的服务运行状况事件时，或用户或应用程序在订阅中执行操作（例如“删除虚拟机”）时，这些警报可发出通知。

若要接收可通过 Azure Monitor 获得的可用诊断日志数据，请将数据路由到 Log Analytics（前身为 Operations Management Suite）并使用 Log Analytics 查询警报。 Log Analytics 现使用[新的警报方法](monitoring-overview-unified-alerts.md)。 

下图总结了 Azure Monitor 中的数据源，并揭示了如何发出此数据的警报。

![警报介绍](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Azure Monitor 警报（经典）的分类
Azure 使用以下术语来描述经典警报和及其功能：
* **警报**：符合标准（一个或多个规则或条件）时激活的定义。
* **活动**：满足经典警报定义的标准时出现的状态。
* **已解决**：在先前满足经典警报定义的标准后不再满足该标准时出现的状态。
* **通知**：基于经典警报变为活动状态时采取的操作。
* **操作**：发送给通知接收方的特定调用（例如，发送到 Webhook URL 的电子邮件或帖子）。 通知通常可以触发多个操作。

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>如何接收来自 Azure Monitor 经典警报的通知？
从历史上来看，来自不同服务的 Azure 警报使用自己的内置通知方法。 

现在，Azure Monitor 提供了一个名为“操作组”的可重复使用的通知组。 操作组指定一组接收通知的接收方。 当激活引用操作组的警报时，所有接收方都会收到该通知。 通过此功能，可在多个警报对象中重复使用一组接收方（例如，在线工程师列表）。 操作组通过各种方法支持通知。 这些方法包括发布到 Webhook URL、发送电子邮件、短信和许多其他操作。 有关详细信息，请参阅[在 Azure 门户中创建和管理操作组](monitoring-action-groups.md)。 

旧式经典活动日志警报使用操作组。

但是，旧式指标警报不使用操作组。 可以改为配置以下操作： 
* 将电子邮件通知发送到服务管理员、共同管理员或指定的其他电子邮件地址。
* 调用 Webhook，以便用户启动其他自动化操作

Webhook 可启用自动化和修复，例如使用以下服务：
- Azure 自动化 Runbook
- Azure Functions
- Azure 逻辑应用
- 第三方服务

## <a name="next-steps"></a>后续步骤
使用以下文档获取有关警报规则以及相关配置的信息：

* 详细了解[指标](monitoring-overview-metrics.md)
* [通过 Azure 门户配置经典指标警报](insights-alerts-portal.md)
* [通过 PowerShell 配置经典指标警报](insights-alerts-powershell.md)
* [通过 Azure CLI 配置经典指标警报](insights-alerts-command-line-interface.md)
* [通过 Azure Monitor REST API 配置经典指标警报](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 详细了解[活动日志](monitoring-overview-activity-logs.md)
* [通过 Azure 门户配置活动日志警报](monitoring-activity-log-alerts.md)
* [通过资源管理器配置活动日志警报](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 查看[活动日志警报 webhook 架构](monitoring-activity-log-alerts-webhook.md)
* 详细了解[操作组](monitoring-action-groups.md)
* 配置[较新的警报](monitor-alerts-unified-usage.md)
