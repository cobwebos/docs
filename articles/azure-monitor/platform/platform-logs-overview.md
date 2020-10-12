---
title: Azure 平台日志概述 | Microsoft Docs
description: Azure Monitor 中的日志概述提供了有关 Azure 资源操作的丰富、频繁的数据。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6ea960e93dba634573ec1ef594f1d2c49be57ca9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84945301"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 平台日志概述
平台日志提供 Azure 资源及其所依赖的 Azure 平台的详细诊断和审核信息。 它们是自动生成的，虽然你需要配置某些平台日志，以便将其转发到一个或多个目标进行保留。 本文概述了平台日志，其中包括它们提供什么信息，以及如何配置它们以方便收集和分析。

## <a name="types-of-platform-logs"></a>平台日志的类型
下表列出了在不同 Azure 层提供的特定的平台日志。

| 日志 | 层 | 说明 |
|:---|:---|:---|
| [资源日志](resource-logs.md) | Azure 资源 | 深入了解在 Azure 资源（数据平面）内执行的操作，例如，从 Key Vault 获取机密，或向数据库发出请求。  资源日志的内容因 Azure 服务和资源类型而异。<br><br>资源日志以前称为诊断日志。   |
| [活动日志](activity-log.md) | Azure 订阅 | 了解从外部（管理平台）  对订阅中的每个 Azure 资源执行的操作，以及对服务运行状况事件进行的更新。 通过活动日志，可确定订阅中资源上进行的任何写入操作 (PUT, POST, DELETE) 的“什么操作、谁操作和操作时间”等信息。    每个 Azure 订阅都有一个活动日志。 |
| [Azure Active Directory 日志](../../active-directory/reports-monitoring/overview-reports.md) | Azure 租户 |  包含特定租户的 Azure Active Directory 中的登录活动和更改审核日志的历史记录。   |

> [!NOTE]
> Azure 活动日志主要适用于 Azure Resource Manager 中发生的活动。 它不跟踪使用经典/RDFE 模型的资源。 某些经典资源类型在 Azure Resource Manager 中具有代理资源提供程序（例如 Microsoft.ClassicCompute）。 如果通过 Azure 资源管理器使用这些代理资源提供程序与经典资源类型进行交互，则操作会显示在活动日志中。 如果在 Azure 资源管理器代理外部与经典资源类型进行交互，则操作只会记录在操作日志中。 可以在门户的一个单独部分中浏览操作日志。

![平台日志概述](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>查看平台日志
可以通过不同的选项查看和分析不同的 Azure 平台日志。

- 查看 Azure 门户中的活动日志，并通过 PowerShell 和 CLI 访问事件。 有关详细信息，请参阅[查看活动日志](activity-log.md#view-the-activity-log)。 
- 在 Azure 门户中查看 Azure Active Directory 的安全和活动报表。 请参阅[什么是 Azure Active Directory 报告？](../../active-directory/reports-monitoring/overview-reports.md)  以获取详细信息。
- 资源日志由受支持的 Azure 资源自动生成，但这些资源不能查看，除非你将它们发送到[目标](#destinations)。 

## <a name="destinations"></a>Destinations
可以将平台日志发送到下表中的一个或多个目标，具体取决于监视要求。 通过[创建诊断设置](diagnostic-settings.md)为平台日志配置目标。

| 目标 | 说明 |
|:---|:---|
| Log Analytics 工作区 | 一起分析所有 Azure 资源的日志，并利用提供给 [Azure Monitor 日志](data-platform-logs.md)的所有功能，包括[日志查询](../log-query/log-query-overview.md)和[日志警报](alerts-log.md)。 将日志查询的结果固定到 Azure 仪表板，或将其作为交互式报表的一部分包含在工作簿中。 |  |
| 事件中心 | 向 Azure 外部发送平台日志数据，例如，向第三方 SIEM 或自定义遥测平台发送。
| Azure 存储 | 将日志存档供审核或备份。 |

- 若要详细了解如何为活动日志或资源日志创建诊断设置，请参阅[创建诊断设置以将平台日志和指标发送到不同目标](diagnostic-settings.md)。 
- 若要详细了解如何创建 Azure Active Directory 日志的诊断设置，请参阅以下文章。
  - [将 Azure AD 日志与 Azure Monitor 日志集成](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [教程：将 Azure Active Directory 日志流式传输到 Azure 事件中心](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [教程：将 Azure AD 日志存档到 Azure 存储帐户](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>后续步骤

* [阅读有关活动日志的详细信息](activity-log.md)
* [阅读有关资源日志的详细信息](resource-logs.md)

