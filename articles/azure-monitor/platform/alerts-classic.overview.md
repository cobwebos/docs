---
title: Microsoft Azure 和 Azure Monitor 中的经典警报概述
description: 经典警报将被弃用。 使用警报可以监视 Azure 资源指标、事件或日志，并在符合指定的条件时接收通知。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/19/2018
ms.openlocfilehash: 177c110ce9679f3d564918dfac50b3e3f66f84ce
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893580"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Microsoft Azure 中的经典警报是什么？

> [!NOTE]
> 本文介绍了如何创建旧式经典指标警报。 Azure Monitor 现在支持[较新的近实时指标警报和新的警报体验](../../azure-monitor/platform/alerts-overview.md)。 经典警报已[停](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)用，但对于尚不支持新警报的资源仍有限制。 
>

警报允许配置数据条件，并在条件与最新监视数据匹配时发出通知。

## <a name="old-and-new-alerting-capabilities"></a>新旧警报功能

在过去的 Azure Monitor 中，Application Insights，Log Analytics 和服务运行状况都有单独的警报功能。 随着时间推移，Azure 改进并组合了用户界面和不同的警报方法。 整合仍在进行中。

只能在 Azure 门户中的经典警报用户屏幕中查看经典警报。 可以通过警报屏幕上的“查看经典警报”按钮访问此屏幕。 

 ![Azure 门户中的警报选项](media/alerts-classic.overview/monitor-alert-screen2.png)

与经典警报体验相比，新式警报用户体验具有以下优点：
-   **更好的通知系统** - 所有新式警报均使用操作组，这些组是命名的通知和操作组，可以在多个警报中重复使用。 经典指标警报和旧版 Log Analytics 警报不使用操作组。
-   **统一的创作体验** - 针对 Azure Monitor、Log Analytics 和 Application Insights 中的指标、日志和活动日志的所有警报创建均在一个位置进行。
-   **在 Azure 门户中查看触发的 Log Analytics 警报** - 现在还可以在订阅中查看触发的 Log Analytics 警报。 以前这些警报在单独的门户中。
-   **分开触发的警报和警报规则** - 对警报规则（定义触发警报的条件）和触发的警报（警报规则触发实例）进行了区分，因此操作视图和配置视图是分开的。
-   **更好的工作流** - 全新警报创作体验引导用户完成警报规则配置过程，因此发现需要发出警报的事项变得更容易了。
-   **智能警报整合**和**设置警报状态** - 新式警报包括了自动分组功能，它将类似的警报显示在一起以降低用户界面中的开销。 

与经典指标警报相比，新式指标警报具有以下优点：
-   **延迟降低**：新型指标警报的运行频率可达每分钟一次。 旧式指标警报每 5 分钟方可运行 1 次。 新式警报从问题发生到发出通知或采取操作的延迟更小（3 到 5 分钟）。 旧式警报需要 5 到 15 分组，具体取决于类型。  由于要花费时间来引入日志，日志警报通常有 10 到 15 分钟的延迟，但新式处理方法缩短了该时间。 
-   **支持多维指标**：支持对维度指标发出警报，从而可监视受关注的指标段。
-   **更好地控制指标条件**：可以定义更丰富的警报规则。 新型警报支持监视指标的最大值、最小值、平均值和总值。
-   **综合监视多个指标**：可以使用单个规则监视多个指标（目前最多为两个指标）。 如果两个指标在指定时间段内违反其各自的阈值，则会触发警报。
-   **更好的通知系统**：所有新型警报均使用[操作组](../../azure-monitor/platform/action-groups.md)，这些组是命名的通知和操作组，可以在多个警报中重复使用。  经典指标警报和旧版 Log Analytics 警报不使用操作组。 
-   **日志中的指标**（公共预览版）：进入 Log Analytics 的日志数据现在可以提取并转换为 Azure Monitor 指标，然后就像其他指标一样，基于其发出警报。 有关特定于经典警报的术语，请参阅[警报（经典）](alerts-classic.overview.md)。 


## <a name="classic-alerts-on-azure-monitor-data"></a>关于 Azure Monitor 数据的经典警报
有两种可用的经典警报类型：指标警报和活动日志警报。

* **经典指标警报** - 当指定的指标值越过了分配的阈值时，就会触发此警报。 当越过了该阈值并且满足警报条件时，警报将生成通知。 此时，该警报被视为“已激活”。 它在“已解决”时（即再次越过阈值且不再满足条件时）生成另一个通知。

* **经典活动日志警报** - 当某个活动日志事件条目与筛选条件匹配时将触发的流式处理日志警报。 这些警报只有一个状态，即“已激活”。 警报引擎只是简单地将筛选条件应用于任何新事件。 它不会进行搜索来查找更早的条目。 出现新的服务运行状况事件时，或用户或应用程序在订阅中执行操作（例如“删除虚拟机”）时，这些警报可发出通知。

对于通过 Azure Monitor 提供的资源日志数据，请将数据路由到 Log Analytics 并使用日志查询警报。 Log Analytics 现在使用[新的警报方法](../../azure-monitor/platform/alerts-overview.md) 

下图总结了 Azure Monitor 中的数据源，从概念上总结了从数据取消警报的方法。

![警报介绍](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>警报的分类（经典）
Azure 使用以下术语来描述经典警报和及其功能：
* **警报** - 符合标准（一个或多个规则或条件）时被激活的定义。
* **活动** - 满足经典警报定义的标准时的状态。
* **已解决** - 在先前满足经典警报定义的标准后不再满足该标准的状态。
* **通知** - 经典警报变为活动状态时采取的操作。
* **操作** - 发送给通知接收方的特定通话（例如，通过电子邮件发送地址或发布到 Webhook URL）。 通知通常可以触发多个操作。

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>如何接收来自 Azure Monitor 经典警报的通知？
从历史上来看，来自不同服务的 Azure 警报使用自己的内置通知方法。 

Azure Monitor 创建了一个称为“操作组”的可重复使用的通知组。 操作组指定一组接收通知的接收方。 每当引用操作组的一个警报被激活时，所有接收方都会收到该通知。 操作组允许在多个警报对象中重复使用一组接收方（例如，在线工程师列表）。 除了电子邮件地址、短信号码和大量其他操作外，操作组还通过发布到 Webhook URL 来支持通知。  有关详细信息，请参阅[操作组](../../azure-monitor/platform/action-groups.md)。 

旧式经典活动日志警报使用操作组。

但是，旧式指标警报不使用操作组。 可以改为配置以下操作： 
- 将电子邮件通知发送到服务管理员、共同管理员或指定的其他电子邮件。
- 调用 Webhook，以便用户启动其他自动化操作

Webhook 可实现自动化和修复，例如使用：
- Azure 自动化 Runbook
- Azure 函数
- Azure 逻辑应用
- 第三方服务

## <a name="next-steps"></a>后续步骤
了解警报规则以及如何使用以下工具来配置这些规则：

* 详细了解[指标](data-platform.md)
* [通过 Azure 门户配置经典指标警报](alerts-classic-portal.md)
* 配置[经典指标警报 PowerShell](alerts-classic-portal.md)
* 配置[经典指标警报命令行接口 (CLI)](alerts-classic-portal.md)
* 配置[经典指标警报 Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 详细了解[活动日志](activity-logs-overview.md)
* [通过 Azure 门户配置活动日志警报](activity-log-alerts.md)
* [通过 Resource Manager 配置活动日志警报](alerts-activity-log.md)
* 查看[活动日志警报 webhook 架构](activity-log-alerts-webhook.md)
* 详细了解[操作组](action-groups.md)
* 配置[较新的警报](alerts-metric.md)
