---
title: 获取 Azure App Service 中的资源事件
description: 了解如何通过应用服务应用上的活动日志和事件网格获取资源事件。
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 1fd283f95823a67319dc467a3a1d6251193182da
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124731"
---
# <a name="get-resource-events-in-azure-app-service"></a>获取 Azure App Service 中的资源事件

Azure App Service 提供内置工具来监视资源的状态和运行状况。 资源事件有助于了解对基础 web 应用资源所做的任何更改，并在必要时采取措施。 事件示例包括：缩放实例、对应用程序设置的更新、重启 web 应用等。 在本文中，你将了解如何查看[Azure 活动日志](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)，以及如何启用[事件网格](https://docs.microsoft.com/azure/event-grid/)来监视与应用服务 web 应用相关的资源事件。

> [!NOTE]
> 应用服务与事件网格的集成处于**预览阶段**。 [查看公告了解更多详细信息。](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>查看 Azure 活动日志
Azure 活动日志包含对订阅中的资源执行的操作所发出的资源事件。 Azure 门户和 Azure 资源管理器模板中的用户操作都有助于活动日志捕获的事件。 

适用于应用服务的 Azure 活动日志详细信息，如：
- 对资源执行的操作（例如：应用服务计划）
- 谁启动了该操作
- 操作何时发生
- 操作的状态
- 有助于研究操作的属性值

### <a name="what-can-you-do-with-azure-activity-logs"></a>你可以对 Azure 活动日志执行哪些操作？

可以使用 Azure 门户、PowerShell、REST API 或 CLI 查询 Azure 活动日志。 可以将日志发送到存储帐户、事件中心和 Log Analytics。 你还可以在 Power BI 中进行分析，或创建警报以随时了解资源事件。

[查看和检索 Azure 活动日志事件。](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>将活动日志传送到事件网格

虽然活动日志是基于用户的，但有一个新的[事件网格](https://docs.microsoft.com/azure/event-grid/)与应用服务（预览）的集成，用于记录用户操作和自动事件。 使用事件网格，可以配置处理程序以响应所说的事件。 例如，使用事件网格在每次向 Blob 存储容器添加新照片时，立即触发无服务器功能运行图像分析。

或者，可使用具有逻辑应用的事件网格来处理任何位置的数据，而无需编写代码。 事件网格将数据源与事件处理程序连接。 例如，使用事件网格在每次向 Blob 存储容器添加新照片时，立即触发无服务器功能运行图像分析。

### <a name="supported-event-types"></a>支持的事件类型
| 事件类型 |说明|
| -----------| ------------- |
| Microsoft.web/sites | Webapp |
| BackupOperationCompleted |Webapp 的备份已成功完成|
| BackupOperationFailed | Webapp 的备份失败|
| RestoreOperationStarted |已开始从备份还原|
| RestoreOperationCompleted |从备份还原已成功完成|
| RestoreOperationFailed |从备份还原失败|
| SlotSwapStarted |槽交换已开始|
| SlotSwapCompleted |槽交换已成功完成|
| SlotSwapFailed |槽交换失败|
| SlotSwapWithPreviewStarted |带预览的槽交换已开始|
| SlotSwapWithPreviewCancelled |槽交换与预览失败|
| AppUpdated | |
| 重新启动 | Webapp 已重新启动 |
| 已停止 | Webapp 已停止 |
| ChangedAppSettings | Webapp 上的应用设置已更改 |
| - | - |
| Microsoft.web/serverfarms | （应用服务计划） |
| AspUpdated | 已更新应用服务计划。 事件对象包含已更改的属性的详细信息。 |
| 纵向/横向扩展 | 应用服务计划扩大或缩小。 事件对象包含实例计数。|


## <a name="next-steps"></a><a name="nextsteps"></a> 后续步骤
* [使用 Azure Monitor 查询日志](../azure-monitor/log-query/log-query-overview.md)
* [如何监视 Azure 应用服务](web-sites-monitor.md)
* [在 Visual Studio 中对 Azure 应用服务进行故障排除](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析应用日志](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
