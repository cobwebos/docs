---
title: 通过诊断设置收集 Azure 活动日志（预览版）-Azure Monitor |Microsoft Docs
description: 使用诊断设置将 Azure 活动日志转发到 Azure Monitor 日志、Azure 存储或 Azure 事件中心。
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 12/20/2019
ms.openlocfilehash: e3a8048d8bcb8bb737c511a8b8bb6b57f9e81403
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750978"
---
# <a name="collect-azure-activity-log-with-legacy-settings"></a>收集具有旧设置的 Azure 活动日志
[Azure 活动日志](platform-logs-overview.md)是一个[平台日志](platform-logs-overview.md)，可用于深入了解 Azure 中发生的订阅级别事件。 最近，你创建了一个日志配置文件，用于将活动日志条目发送到[事件中心或存储帐户](activity-log-export.md)，并使用连接器将其收集到[Log Analytics 工作区](activity-log-collect.md)中。 本文介绍方法之间的差异、如何使用现有旧设置，以及如何在准备诊断设置时清除旧设置。


## <a name="differences-between-methods"></a>方法之间的差异

### <a name="advantages"></a>优点
与当前方法相比，使用诊断设置具有以下优势：

- 用于收集所有平台日志的一致方法。
- 跨多个订阅和租户收集活动日志。
- 筛选集合以仅收集特定类别的日志。
- 收集所有活动日志类别。 某些类别不是使用旧方法收集的。
- 日志引入延迟更快。 前一种方法的延迟大约为15分钟，诊断设置仅添加大约1分钟的时间。
  
### <a name="considerations"></a>注意事项
在启用此功能之前，请考虑使用诊断设置的活动日志收集的以下详细信息。

- 删除了用于将活动日志收集到 Azure 存储的保留设置，这意味着数据将无限期存储，直到将其删除。
- 目前只能使用 Azure 门户创建订阅级别的诊断设置。 若要使用其他方法（例如 PowerShell 或 CLI），可以创建一个资源管理器模板。


### <a name="differences-in-data"></a>数据差异
诊断设置收集的数据与之前用于收集活动日志的方法相同，但其当前差异如下：

以下属性已删除：

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

添加了以下属性：

- Authorization_d
- Claims_d
- Properties_d

## <a name="work-with-legacy-settings"></a>使用旧设置
如果未选择使用诊断设置替换，则收集活动日志的旧设置将继续工作。 使用以下方法来管理订阅的日志配置文件。

1. 从 Azure 门户的 " **Azure Monitor** " 菜单中，选择 "**活动日志**"。
3. 单击“诊断设置”。
   
   ![诊断设置](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. 单击紫色横幅了解旧体验。 

    ![旧体验](media/diagnostic-settings-subscription/legacy-experience.png)


有关使用旧收集方法的详细信息，请参阅以下文章。

- [收集和分析 Azure Monitor 中 Log Analytics 工作区中的 Azure 活动日志](activity-log-collect.md)
- [跨 Azure Active Directory 租户将 Azure 活动日志收集到 Azure Monitor](activity-log-collect-tenants.md)
- [将 Azure 活动日志导出到存储或 Azure 事件中心](activity-log-export.md)

## <a name="disable-existing-settings"></a>禁用现有设置
你应禁用活动的现有集合，然后才能使用诊断设置启用它。 同时启用这两个可能会导致数据重复。

### <a name="disable-collection-into-log-analytics-workspace"></a>禁用集合到 Log Analytics 工作区

1. 打开 Azure 门户中的 " **Log Analytics 工作区**" 菜单，然后选择工作区以收集活动日志。
2. 在工作区菜单的 "**工作区数据源**" 部分中，选择 " **Azure 活动日志**"。
3. 单击要断开连接的订阅。
4. 单击 "**断开连接**"，然后单击 **"是"** 以确认你的选择。

### <a name="disable-log-profile"></a>禁用日志配置文件

1. 使用使用[旧设置](#work-with-legacy-settings)中所述的过程打开旧设置。
2. 禁用任何当前集合到存储或事件中心。 



## <a name="activity-log-monitoring-solution"></a>活动日志监视解决方案
Azure Log Analytics 监视解决方案包含多个日志查询和视图，用于分析 Log Analytics 工作区中的活动日志记录。 如果你使用诊断设置收集活动日志，此解决方案将使用在 Log Analytics 工作区中收集的日志数据并将继续工作，而无需进行任何更改。 有关此解决方案的详细信息，请参阅[活动日志分析监视解决方案](activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

## <a name="next-steps"></a>后续步骤

* [了解有关活动日志的更多信息](../../azure-resource-manager/resource-group-audit.md)
* [了解有关诊断设置的详细信息](diagnostic-settings.md)
