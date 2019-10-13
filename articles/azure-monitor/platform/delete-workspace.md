---
title: 删除和还原 Azure Log Analytics 工作区 |Microsoft Docs
description: 了解在个人订阅中创建 Log Analytics 工作区后如何删除它，以及如何重构工作区模型。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: magoedte
ms.openlocfilehash: 4f03fc71a11c1ecb2e96b316efac9249395fc333
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285546"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>删除和还原 Azure Log Analytics 工作区
本文介绍 Azure Log Analytics 工作区软删除的概念，以及如何恢复已删除的工作区。 

## <a name="considerations-when-deleting-a-workspace"></a>删除工作区时的注意事项
删除 Log Analytics 工作区时，将执行软删除操作，以允许在14天内恢复工作区（包括其数据和连接的代理），无论是意外删除还是有意删除。 软删除期之后，工作区及其数据将无法恢复，并将在30天内排队等待永久性删除。

删除工作区时要格外小心，因为可能会对你的服务操作造成重要的数据和配置。 查看哪些代理、解决方案以及将其数据存储在 Log Analytics 中的其他 Azure 服务和源，例如：
* 管理解决方案
* Azure 自动化
* 在 Windows 和 Linux 虚拟机上运行的代理
* 在环境中于 Windows 和 Linux 计算机上运行的代理
* System Center Operations Manager

软删除操作会删除工作区资源，并且任何关联的用户的权限都将中断。 如果用户与其他工作区相关联，则他们可以继续使用 Log Analytics 与其他工作区。

## <a name="soft-delete-behavior"></a>软删除行为
工作区删除操作会删除工作区资源管理器资源，但其配置和数据将保留14天，同时提供工作区删除的外观。 在软删除期间，配置为向工作区报告的任何代理和 System Center Operations Manager 管理组都将保持孤立状态。 该服务还提供了一种机制，用于恢复已删除的工作区（包括其数据和连接的资源），实质上是撤消删除操作。

> [!NOTE] 
> 在删除时，将从工作区中永久删除已安装的解决方案和类似于自动化帐户的链接服务，且无法恢复。 应在恢复操作后重新配置这些配置，使工作区成为其以前的功能。 

可以使用[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0)、 [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)或[Azure 门户](https://portal.azure.com)来删除工作区。

### <a name="delete-workspace-in-azure-portal"></a>删除 Azure 门户中的工作区
1. 若要登录，请转到 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。
3. 在 Log Analytics 工作区列表中，选择一个工作区，然后单击中间窗格顶部的 "**删除**"。
   ![从工作区属性窗格中删除选项](media/delete-workspace/log-analytics-delete-workspace.png)
4. 显示询问是否确实要删除工作区的确认消息窗口时，单击“是”。
   ![确认删除工作区](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>恢复工作区
如果对工作区在软删除操作之前关联到的订阅和资源组具有 "参与者" 权限，则可以在其软删除期间（包括其数据、配置和连接的代理）恢复该工作区。 软删除期结束后，工作区将不可恢复并分配给永久删除。

可以通过使用[PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace)或[REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate)工作区创建方法来重新创建工作区，前提是这些属性使用已删除工作区的详细信息进行填充，其中包括：
1.  订阅 ID
2.  资源组名称
3.  工作区名称
4.  地区

> [!NOTE]
> * [Azure 门户](https://portal.azure.com)中不支持工作区恢复。 软删除期间重新创建工作区会显示此工作区名称已被使用。
> * 已删除工作区的名称将保留软删除期间，不能在创建新工作区时使用。
