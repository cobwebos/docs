---
title: 获取 Azure 应用服务中的资源事件
description: 了解如何通过活动日志和事件网格获取应用服务应用上的资源事件。
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c5c84891187c540c0b24162cf5c8c7f96e9e731a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962292"
---
# <a name="get-resource-events-in-azure-app-service"></a>获取 Azure 应用服务中的资源事件

Azure 应用服务提供用于监视资源的状态和运行状况的内置工具。 资源事件可帮助你了解对基础 Web 应用资源所做的任何更改，并在必要时采取措施。 事件示例包括：缩放实例、更新应用程序设置、重启 Web 应用等。 本文介绍如何查看 [Azure 活动日志](../azure-monitor/platform/activity-log.md#view-the-activity-log)并启用[事件网格](../event-grid/index.yml)，监视与应用服务 Web 应用相关的资源事件。

> [!NOTE]
> 应用服务与事件网格的集成目前处于预览阶段。 [查看公告以了解更多详细信息。](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>查看 Azure 活动日志
Azure 活动日志包含对订阅中的资源执行的操作所发出的资源事件。 Azure 门户和 Azure 资源管理器模板中的用户操作都会影响活动日志捕获的事件。 

应用服务的 Azure 活动日志详细信息如下：
- 对资源执行了什么操作（例如：应用服务计划）
- 谁启动了该操作
- 操作何时发生
- 操作的状态
- 有助于调查操作的属性值

### <a name="what-can-you-do-with-azure-activity-logs"></a>可以对 Azure 活动日志执行哪些操作？

可以使用 Azure 门户、PowerShell、REST API 或 CLI 查询 Azure 活动日志。 可以将日志发送到存储帐户、事件中心和 Log Analytics。 还可以在 Power BI 中分析这些日志或创建警报以随时了解资源事件。

[查看和检索 Azure 活动日志事件。](../azure-monitor/platform/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>将活动日志传送到事件网格

虽然活动日志是以用户为基础的，但我们推出了一个新的[事件网格](../event-grid/index.yml)与应用服务的集成（预览版），可用于记录用户操作和自动化事件。 使用事件网格，可以配置处理程序以响应所述事件。 例如，使用事件网格在每次向 Blob 存储容器添加新照片时，立即触发无服务器功能运行图像分析。

或者，可使用具有逻辑应用的事件网格来处理任何位置的数据，而无需编写代码。 事件网格将数据源与事件处理程序连接。 例如，使用事件网格在每次向 Blob 存储容器添加新照片时，立即触发无服务器功能运行图像分析。

[查看 Azure 应用服务事件的属性和架构。](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> 后续步骤
* [使用 Azure Monitor 查询日志](../azure-monitor/log-query/log-query-overview.md)
* [如何监视 Azure 应用服务](web-sites-monitor.md)
* [在 Visual Studio 中对 Azure 应用服务进行故障排除](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析应用日志](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)