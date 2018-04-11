---
title: Microsoft Azure 和 Azure Monitor 中的经典警报概述 | Microsoft Docs
description: 使用警报可以监视 Azure 资源指标、事件或日志，并在符合指定的条件时接收通知。
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Microsoft Azure 中的经典警报是什么？

> [!NOTE]
> 本文介绍了如何创建旧式经典指标警报。 Azure Monitor 现在支持[新式近实时指标警报](monitoring-overview-unified-alerts.md)
>

本文介绍了 Microsoft Azure 中的各种警报来源、这些警报的目的、警报优点以及警报的使用方法。 本文的内容专门与 Azure Monitor 经典警报相关。 警报提供了一种在 Azure 中监视的方法，允许配置数据条件，并在条件与最新监视数据匹配时发出通知。


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Azure Monitor 经典警报的分类
Azure 使用以下术语来描述经典警报和及其功能：
* **警报** - 符合标准（一个或多个规则或条件）时被激活的定义。
* **活动** - 满足经典警报定义的标准时的状态。
* **已解决** - 在先前满足经典警报定义的标准后不再满足该标准的状态。
* **通知** - 经典警报变为活动状态时采取的操作。
* **操作** - 发送给通知接收方的特定通话（例如，通过电子邮件发送地址或发布到 Webhook URL）。 通知通常可以触发多个操作。


## <a name="alerts-on-azure-monitor-data"></a>关于 Azure Monitor 数据的警报
Azure Monitor 提供的数据有三种类型的警报 - 指标警报、准实时指标警报和活动日志警报。

* **经典指标警报** - 当指定的指标值越过了分配的阈值时，就会触发此警报。 当警报“激活”（当阈值越过并满足警报条件时）以及“已解决”（当阈值再次超过并且不再满足条件）时，警报将生成通知。 这些是较旧的指标警报。 有关较新的指标警报，请参阅下文。

* **近实时指标警报**（新式警报体验）- 与以前的指标警报相比，这些新一代指标警报的功能已经过改进。 这些警报可以 1 分钟的频率运行，还支持监视多个（目前是两个）指标。  当警报“激活”（同时越过每个指标的阈值并满足警报条件时）以及“已解决”（至少一个指标再次超过阈值并且不再满足条件）时，警报将生成通知。

* **经典活动日志警报** - 当生成与分配的筛选器条件匹配的活动日志事件时，触发的流式处理日志警报。 这些警报只有“已激活”这一个状态，因为警报引擎只需将筛选器条件应用到任何新事件。 出现新的服务运行状况事件时，或用户或应用程序在订阅中执行操作（例如“删除虚拟机”）时，可以使用这些警报通知。

对于通过 Azure Monitor 提供的诊断日志数据，建议将数据路由到 Log Analytics 并使用 Log Analytics 警报。 下图总结了 Azure Monitor 中的数据源，从概念上总结了从数据取消警报的方法。

![警报介绍](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>如何接收有关 Azure Monitor 经典警报的通知？
从历史上来看，来自不同服务的 Azure 警报使用自己的内置通知方法。 今后，Azure Monitor 提供了一个名为“操作组”的可重复使用的通知组。 操作组指定一组通知接收方（任意数量的电子邮件地址、用于接收短信的电话号码或 Webhook URL），任何时间引用操作组时都会激活警报，所有接收方都会收到该通知。 可在多个警报对象中重复使用一组接收方（例如，在线工程师列表）。 目前，只有活动日志警报可以使用操作组，但还有几个其他 Azure 警报类型也在研究如何使用操作组。

操作组除通过电子邮件地址和短信号码通知外，还可发布到 Webhook URL 来支持通知。 这可实现自动化和修复，例如使用：
    - Azure 自动化 Runbook
    - Azure 函数
    - Azure 逻辑应用
    - 第三方服务

准实时指标警报和活动日志警报使用操作组。

“警报”下可用的旧指标警报（经典）不使用操作组。 在单个指标警报中，可将通知配置为：
* 将电子邮件通知发送到服务管理员、共同管理员或指定的其他电子邮件。
* 调用 Webhook，以便用户启动其他自动化操作

## <a name="next-steps"></a>后续步骤
了解警报规则以及如何使用以下工具来配置这些规则：

* 详细了解[指标](monitoring-overview-metrics.md)
* [通过 Azure 门户配置经典指标警报](insights-alerts-portal.md)
* 配置[经典指标警报 PowerShell](insights-alerts-powershell.md)
* 配置[经典指标警报命令行接口 (CLI)](insights-alerts-command-line-interface.md)
* 配置[经典指标警报 Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 详细了解[活动日志](monitoring-overview-activity-logs.md)
* [通过 Azure 门户配置活动日志警报](monitoring-activity-log-alerts.md)
* [通过 Resource Manager 配置活动日志警报](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 查看[活动日志警报 webhook 架构](monitoring-activity-log-alerts-webhook.md)
* 了解有关[较新指标警报](monitoring-near-real-time-metric-alerts.md)的详细信息
* 详细了解[服务通知](monitoring-service-notifications.md)
* 详细了解[操作组](monitoring-action-groups.md)
* 配置[警报](monitor-alerts-unified-usage.md)
