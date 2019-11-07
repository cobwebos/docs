---
title: 通过诊断设置收集 Azure 活动日志（预览版）-Azure Monitor |Microsoft Docs
description: 使用诊断设置将 Azure 活动日志转发到 Azure Monitor 日志、Azure 存储或 Azure 事件中心。
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 9f8783dc6d3c14b086364639b60273dbae626cee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587974"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>通过诊断设置收集 Azure 活动日志（预览）
[Azure 活动日志](activity-logs-overview.md)是一个[平台日志](platform-logs-overview.md)，可用于深入了解 Azure 中发生的订阅级别事件。 到现在为止，你已经创建了一个日志配置文件，用于将活动日志条目发送到[事件中心或存储帐户](activity-log-export.md)，并使用连接器将它们收集到[Log Analytics 工作区](activity-log-collect.md)中。

你现在可以使用用于收集[资源日志](resource-logs-overview.md)的相同[诊断设置](diagnostic-settings.md)来配置 Azure 活动日志的收集。 与当前方法相比，使用诊断设置具有以下优势：

- 用于收集所有平台日志的一致方法。
- 跨多个订阅和租户收集活动日志。
- 筛选集合以仅收集特定类别的日志。

## <a name="considerations"></a>注意事项
在启用此功能之前，请考虑使用诊断设置的活动日志收集的以下详细信息。

- 你应禁用活动的现有集合，然后才能使用诊断设置启用它。 同时启用这两个可能会导致数据重复。
- 删除了用于将活动日志收集到 Azure 存储的保留设置，这意味着数据将无限期存储，直到将其删除。
- 可以将多个订阅中的活动日志发送到单个 Log Analytics 工作区。 可以将任何单个订阅中的活动日志发送到最多五个 Log Analytics 工作区。

## <a name="configure-diagnostic-settings"></a>配置诊断设置
使用以下过程在 Azure 门户中创建诊断设置以收集 Azure 活动日志。 当前无法使用其他方法创建订阅级别的诊断设置。

1. 禁用活动日志的现有 Log Analytics 工作区集合：
   1. 打开 Azure 门户中的 " **Log Analytics 工作区**" 菜单，然后选择工作区以收集活动日志。
   2. 在工作区的菜单的“工作区数据源”部分，选择“Azure 活动日志”。
   3. 单击要断开连接的订阅。
   4. 单击 "**断开连接**"，然后单击 **"是"** 以确认你的选择。
2. 从 Azure 门户的 " **Azure Monitor** " 菜单中，选择 "**活动日志**"。
3. 单击“诊断设置”。
   
   ![诊断设置](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. 单击紫色横幅了解旧体验，并禁用任何当前集合到存储或事件中心。 

    ![旧体验](media/diagnostic-settings-subscription/legacy-experience.png)

5. 按照 "创建诊断[设置" Azure 门户](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)中的过程创建诊断设置。 有关可用于从活动日志中筛选事件的类别的说明，请参阅[活动日志中的类别](activity-logs-overview.md#categories-in-the-activity-log)。 


## <a name="differences-in-data"></a>数据差异
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

## <a name="activity-log-monitoring-solution"></a>活动日志监视解决方案
Azure Log Analytics 监视解决方案包含多个日志查询和视图，用于分析 Log Analytics 工作区中的活动日志记录。 如果你使用诊断设置收集活动日志，此解决方案将使用在 Log Analytics 工作区中收集的日志数据并将继续工作，而无需进行任何更改。 有关此解决方案的详细信息，请参阅[活动日志分析监视解决方案](activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

## <a name="next-steps"></a>后续步骤

* [了解有关活动日志的更多信息](../../azure-resource-manager/resource-group-audit.md)
* [了解有关诊断设置的详细信息](diagnostic-settings.md)
