---
title: Azure 平台日志概述 |Microsoft Docs
description: Azure Monitor 中的日志概述，提供有关 Azure 资源操作的丰富、频繁的数据。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 480c028f11de9a7c44168b217ad3553d721d01e1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894550"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 平台日志概述
平台日志提供有关 Azure 资源及其依赖的 Azure 平台的详细诊断和审核信息。 它们是自动生成的，不过你需要将某些平台日志配置为转发到要保留的一个或多个目标。 本文概述了平台日志，包括这些日志提供的信息以及如何将它们配置为收集和分析。

## <a name="types-of-platform-logs"></a>平台日志的类型
下表列出了在不同的 Azure 层提供的特定平台日志。

| 层 | 日志 | 描述 |
|:---|:---|:---|
| Azure 资源 | [资源日志](resource-logs-overview.md) | 提供对 Azure 资源（*数据平面*）中执行的操作的深入了解，例如从 Key Vault 获取机密或向数据库发出请求。 资源日志的内容因 Azure 服务和资源类型而异。<br>*资源日志以前称为诊断日志。*  |
| Azure 订阅 | [活动日志](activity-logs-overview.md) | 提供对除服务运行状况事件更新之外的订阅中的每个 Azure资源上的操作的见解。 每个 Azure 订阅都有一个活动日志。   |
| Azure 租户 | [Azure Active Directory 日志](../../active-directory/reports-monitoring/overview-reports.md)  | 包含登录活动的历史记录，以及在特定租户的 Azure Active Directory 中做出的更改的审核跟踪。   |


![平台日志概述](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>查看平台日志
您可以查看[活动日志](activity-log-view.md)，并[Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) Azure 门户中的日志。 您必须向[目标](#destinations)发送资源日志以查看它们。


## <a name="destinations"></a>Destinations
你可以根据监视要求将平台日志发送到下表中的一个或多个目标。 

| 目标 | 场景 | 参考 |
|:---|:---|:---|:---|
| Log Analytics 工作区 | 使用其他监视数据分析日志，并利用日志查询和警报等 Azure Monitor 功能。 | [资源日志](resource-logs-collect-storage.md)<br>[活动日志](activity-log-collect.md)<br>[Azure 活动目录日志](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 存储 | 存档日志以进行审核、静态分析或备份。 |[资源日志](archive-diagnostic-logs.md)<br>[活动日志](activity-log-export.md)<br>[Azure 活动目录日志](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中心 | 将日志流式传输到第三方日志记录和遥测系统。  |[资源日志](resource-logs-stream-event-hubs.md)<br>[活动日志](activity-log-export.md)<br>[Azure 活动目录日志](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>诊断设置和日志配置文件
通过[创建诊断设置](diagnostic-settings.md)，配置资源日志的目标并 Azure Active Directory 日志。 通过[创建日志配置文件](activity-log-export.md)或将[其连接到 Log Analytics 工作区](activity-log-collect.md)来配置活动日志的目标。

诊断设置和日志配置文件定义以下各项：

- 用于发送选定日志和指标的一个或多个目标。
- 资源的日志类别和指标将发送到目标。
- 如果选择一个存储帐户作为目标，则每个日志类别应保留多长时间。



## <a name="next-steps"></a>后续步骤

* [了解有关活动日志的详细信息](activity-logs-overview.md)
* [了解有关资源日志的详细信息](resource-logs-overview.md)
* [查看不同 Azure 服务的资源日志架构](diagnostic-logs-schema.md)
