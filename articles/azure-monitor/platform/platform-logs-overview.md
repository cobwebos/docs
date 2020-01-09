---
title: Azure 平台日志概述 |Microsoft Docs
description: Azure Monitor 中的日志概述，提供有关 Azure 资源操作的丰富、频繁的数据。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 5f02368bfb0c084691376300980d4cdee0d9b3be
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530878"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 平台日志概述
平台日志提供有关 Azure 资源及其依赖的 Azure 平台的详细诊断和审核信息。 它们是自动生成的，不过你需要将某些平台日志配置为转发到要保留的一个或多个目标。 本文概述了平台日志，包括这些日志提供的信息以及如何将它们配置为收集和分析。

## <a name="types-of-platform-logs"></a>平台日志的类型
下表列出了在不同的 Azure 层提供的特定平台日志。

| 日志 | 层 | Description |
|:---|:---|:---|
| 资源日志 | Azure 资源 | 提供对 Azure 资源（*数据平面*）中执行的操作的深入了解，例如从 Key Vault 获取机密或向数据库发出请求。 资源日志的内容因 Azure 服务和资源类型而异。<br><br>*资源日志以前称为诊断日志。*  |
| 活动日志 | Azure 订阅 | 提供对除服务运行状况事件更新之外的订阅中的每个 Azure资源上的操作的见解。 使用活动日志来_确定对订阅_中的资源_执行的任何_写入操作（ _PUT、POST_、DELETE）。 还可以了解该操作和其他相关属性的状态。  每个 Azure 订阅都有一个活动日志。 |
| Azure Active Directory 日志 | Azure 租户 |  包含登录活动的历史记录，以及在特定租户的 Azure Active Directory 中做出的更改的审核跟踪。 有关 Azure Active Directory 日志的完整说明，请参阅[什么是 Azure Active Directory 报表？](../../active-directory/reports-monitoring/overview-reports.md) 。   |

> [!NOTE]
> Azure 活动日志主要用于在 Azure 资源管理器中发生的活动。 它不跟踪使用经典/RDFE 模型的资源。 某些经典资源类型在 Azure 资源管理器中具有代理资源提供程序（例如 Microsoft.ClassicCompute）。 如果通过 Azure 资源管理器使用这些代理资源提供程序与经典资源类型进行交互，则操作会显示在活动日志中。 如果在 Azure 资源管理器代理外部与经典资源类型进行交互，则操作只会记录在操作日志中。 可以在门户的一个单独部分中浏览操作日志。

![平台日志概述](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>查看平台日志
查看和分析不同的 Azure 平台日志有不同的选项。

- 查看 Azure 门户中的活动日志，并从 PowerShell 和 CLI 访问事件。 有关详细信息，请参阅[查看和检索 Azure 活动日志事件](activity-log-view.md)。 
- 查看 Azure 门户中 Azure Active Directory 安全和活动报告。 请参阅[什么是 Azure Active Directory 报表？](../../active-directory/reports-monitoring/overview-reports.md)  了解详细信息。
- 资源日志是由受支持的 Azure 资源自动生成的，但只有在将它们发送到[目标](#destinations)时才能查看。 

## <a name="destinations"></a>Destinations
你可以根据监视要求将平台日志发送到下表中的一个或多个目标。 通过[创建诊断设置](diagnostic-settings.md)来配置平台日志的目标。

| 目标 | 方案 | 参考 |
|:---|:---|:---|:---|
| Log Analytics 工作区 | 使用其他监视数据分析日志，并利用日志查询和警报等 Azure Monitor 功能。 | [活动日志和资源日志](resource-logs-collect-workspace.md)<br>[Azure 活动目录日志](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 存储 | 存档日志以进行审核、静态分析或备份。 |[活动日志和资源日志](archive-diagnostic-logs.md)<br>[Azure 活动目录日志](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中心 | 将日志流式传输到第三方日志记录和遥测系统。  |[活动日志和资源日志](resource-logs-stream-event-hubs.md)<br>[Azure 活动目录日志](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>后续步骤

* [查看不同类别的活动日志架构](activity-log-schema.md)
* [查看不同 Azure 服务的资源日志架构](diagnostic-logs-schema.md)
