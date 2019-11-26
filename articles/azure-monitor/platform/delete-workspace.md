---
title: 删除和恢复 Azure Log Analytics 工作区 | Microsoft Docs
description: 了解在个人订阅中创建 Log Analytics 工作区后如何删除它，以及如何重构工作区模型。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/28/2019
ms.openlocfilehash: b8fdefb5e8555e90b5c9065672f4593e5bf98e06
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326499"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>删除和还原 Azure Log Analytics 工作区

本文介绍 Azure Log Analytics 工作区软删除的概念以及如何恢复已删除的工作区。 

## <a name="considerations-when-deleting-a-workspace"></a>删除工作区时的注意事项

当你删除 Log Analytics 工作区时，系统会执行软删除操作，目的是让你能够在 14 天内恢复工作区（包括其数据和连接的代理），不管该删除是意外删除还是有意删除。 软删除期间之后，工作区及其数据是不可恢复的–数据在30天内排队等待永久性删除，并且可用于创建新的工作区。

删除工作区时需谨慎，因为其中的重要数据和配置在删除后可能会对服务操作产生不利影响。 请了解那些将数据存储在 Log Analytics 中的代理、解决方案以及其他 Azure 服务和源，例如：

* 管理解决方案
* Azure 自动化
* 在 Windows 和 Linux 虚拟机上运行的代理
* 在环境中于 Windows 和 Linux 计算机上运行的代理
* System Center Operations Manager

软删除操作会删除工作区资源，并且任何关联的用户的权限都将中断。 如果用户与其他工作区关联，他们可以继续通过其他工作区使用 Log Analytics。

## <a name="soft-delete-behavior"></a>软删除行为

工作区软删除操作会删除工作区资源管理器资源，但会保留其配置和数据 14 天，给人的印象是该工作区已删除。 在软删除期间，被配置为向工作区报告的任何代理和 System Center Operations Manager 管理组持续处于孤立状态。 该服务还提供了用于恢复已删除工作区（包括其数据和连接的资源）的机制（实质上是撤消删除）。

> [!NOTE] 
> 在删除时，将从工作区中永久删除已安装的解决方案和类似于你的 Azure 自动化帐户的链接服务，且无法恢复。 这些内容应该在恢复操作后重新进行配置，使工作区回到以前配置的状态。

可以通过 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0)、[REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) 或 [Azure 门户](https://portal.azure.com)删除工作区。

### <a name="delete-workspace-in-azure-portal"></a>在 Azure 门户中删除工作区

1. 若要登录，请转到 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入 **Log Analytics**。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。
3. 在 Log Analytics 工作区列表中选择一个工作区，然后从中间窗格的顶端单击“删除”。
   ![从工作区属性窗格中删除选项](media/delete-workspace/log-analytics-delete-workspace.png)
4. 显示询问是否确实要删除工作区的确认消息窗口时，单击“是”。
   ![确认删除工作区](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>恢复工作区

如果你有订阅和资源组（其中的工作区在软删除操作之前已进行关联）的“参与者”权限，则可在软删除期间恢复它（包括其数据、配置和连接的代理）。 软删除期过后，工作区将不可恢复，会被系统指定进行永久删除。 已删除工作区的名称会在软删除期间保留，不能用于创建新工作区。  

你可以使用以下工作区 create 方法恢复工作区： [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace)或[REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) ，前提是以下属性使用已删除工作区的详细信息进行填充：

* 订阅 ID
* 资源组名称
* 工作区名称
* 地区

执行恢复操作后，工作区及其所有数据就会恢复。 删除工作区时，会将其中的解决方案和关联的服务永久删除，因此应重新配置它们，使工作区回到以前配置的状态。 工作区恢复以后，某些数据可能不能用于查询，必须等到重新安装关联的解决方案并将其架构添加到工作区为止。

> [!NOTE]
> * 不支持在 [Azure 门户](https://portal.azure.com)中恢复工作区。 
> * 在软删除期间重新创建工作区表明该工作区名称已进入使用状态。 
> 
