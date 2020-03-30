---
title: 使用诊断设置收集 Azure 活动日志 - Azure 监视器 |微软文档
description: 使用诊断设置，将 Azure 活动日志转发到 Azure Monitor 日志、Azure 存储或 Azure 事件中心。
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096898"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>更新到 Azure 活动日志集合和导出
[Azure 活动日志](platform-logs-overview.md)是一种方便用户深入了解 Azure 中发生的订阅级别事件的[平台日志](platform-logs-overview.md)。 将活动日志条目发送到[事件中心或存储帐户](activity-log-export.md)或[日志分析工作区](activity-log-collect.md)的方法已更改为使用[诊断设置](diagnostic-settings.md)。 本文介绍了方法之间的区别以及如何清除旧版设置，以准备更改为诊断设置。


## <a name="differences-between-methods"></a>各方法之间的区别

### <a name="advantages"></a>优点
与当前方法相比，使用诊断设置具有以下优势：

- 用于收集所有平台日志的一致方法。
- 跨多个订阅和租户收集活动日志。
- 筛选集合，以便仅收集特定类别的日志。
- 收集所有活动日志类别。 部分类别不是使用旧版方法收集的。
- 更短的日志引入延迟。 以前的方法的延迟大约为 15 分钟，而诊断设置仅会增加约 1 分钟的延迟。

### <a name="considerations"></a>注意事项
启用此功能之前，请考虑使用诊断设置的活动日志集合的以下详细信息。

- 已删除将活动日志收集到 Azure 存储的保留期设置，这意味着数据在删除之前将无限期存储。
- 目前只能使用 Azure 门户创建订阅级别的诊断设置。 若要使用 PowerShell 或 CLI 等其他方法，可以创建资源管理器模板。


### <a name="differences-in-data"></a>数据区别
诊断设置收集的数据与之前收集活动日志的方法所收集的数据相同，但目前存在以下差异：

已删除以下列。 这些列的替换采用不同的格式，因此您可能需要修改使用它们的日志查询。 您仍然可以在架构中看到已删除的列，但它们不会被数据填充。

| 删除的列 | 替换列 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | 活动子状态值 |
| OperationName     | 操作名称值     |
| ResourceProvider  | 资源提供者价值  |

已添加以下列：

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> 在某些情况下，这些列中的值可能在所有大写中。 如果查询包含这些列，则应使用[* 运算符](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators)执行不区分大小写的比较。

## <a name="work-with-legacy-settings"></a>使用旧版设置
如果未选择使用诊断设置替换旧版设置，则旧版设置将继续收集活动日志。 使用以下方法管理订阅的日志配置文件。

1. 从 Azure 门户上的 Azure Monitor 菜单中，选择“活动日志”********。
3. 单击 **"诊断设置**"。

   ![诊断设置](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 单击紫色横幅了解旧版体验。

    ![旧版体验](media/diagnostic-settings-subscription/legacy-experience.png)


有关使用旧版集合方法的详细信息，请参阅以下文章。

- [收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](activity-log-collect.md)
- [跨 Azure Active Directory 租户将 Azure 活动日志收集到 Azure Monitor 中](activity-log-collect-tenants.md)
- [将 Azure 活动日志导出到存储或 Azure 事件中心](activity-log-export.md)

## <a name="disable-existing-settings"></a>禁用现有设置
应禁用活动的现有集合，然后才能使用诊断设置。 同时启用两者可能会导致数据重复。

### <a name="disable-collection-into-log-analytics-workspace"></a>禁用到 Log Analytics 工作区的集合

1. 打开 Azure 门户中的“Log Analytics 工作区”菜单，然后选择要收集活动日志的工作区****。
2. 在工作区的菜单的“工作区数据源”部分，选择“Azure 活动日志”。********
3. 单击要断开连接的订阅。
4. 单击“断开连接”，然后在系统提示进行确认时单击“确定”********。

### <a name="disable-log-profile"></a>禁用日志配置文件

1. 按照[使用旧版设置](#work-with-legacy-settings)中所述流程打开旧版设置。
2. 禁用所有当前到存储或事件中心的集合。



## <a name="activity-log-monitoring-solution"></a>活动日志监视解决方案
Azure Log Analytics 监视解决方案包含多个日志查询和视图，用于分析 Log Analytics 工作区中的活动日志记录。 如果使用诊断设置收集活动日志，则此解决方案会使用 Log Analytics 工作区中收集的日志数据，并在不进行任何更改的情况下继续生效。 有关此解决方案的详细信息，请参阅[活动日志分析监视解决方案](activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

## <a name="next-steps"></a>后续步骤

* [了解有关活动日志的更多内容](../../azure-resource-manager/management/view-activity-logs.md)
* [详细了解诊断设置](diagnostic-settings.md)
