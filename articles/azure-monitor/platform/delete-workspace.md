---
title: 删除和恢复 Azure Log Analytics 工作区 |Microsoft Docs
description: 了解在个人订阅中创建 Log Analytics 工作区后如何删除它，以及如何重构工作区模型。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 03be29cde42478abf32492f55a296aeee0a4a478
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547245"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>删除和还原 Azure Log Analytics 工作区

本文介绍 Azure Log Analytics 工作区软删除的概念，以及如何恢复已删除的工作区。 

## <a name="considerations-when-deleting-a-workspace"></a>删除工作区时的注意事项

删除 Log Analytics 工作区时，将执行软删除操作，以允许在14天内恢复工作区（包括其数据和连接的代理），无论是意外删除还是有意删除。 软删除期间之后，工作区资源及其数据是不可恢复的，其数据将排队等待永久删除并在30天内完全清除。 工作区名称为 "已发布"，你可以使用它来创建新的工作区。

> [!NOTE]
> 无法关闭软删除行为。 稍后，我们将添加一个选项，以在删除操作中使用 "force" 标记时替代软删除。

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
> 在删除时，将从工作区中永久删除已安装的解决方案和类似于你的 Azure 自动化帐户的链接服务，且无法恢复。 应在恢复操作后重新配置这些配置，使工作区进入其以前配置的状态。

你可以使用[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0)、 [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)或[Azure 门户](https://portal.azure.com)中删除工作区。

### <a name="azure-portal"></a>Azure 门户

1. 若要登录，请前往[Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。
3. 在 Log Analytics 工作区列表中，选择一个工作区，然后单击中间窗格顶部的 "**删除**"。
   ![从工作区属性窗格中删除选项](media/delete-workspace/log-analytics-delete-workspace.png)
4. 显示询问是否确实要删除工作区的确认消息窗口时，单击“是”。
   ![确认删除工作区](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>永久删除工作区
在某些情况下（如开发和测试），软删除方法可能不适合，需要使用相同的设置和工作区名称重复部署。 在这种情况下，你可以永久删除你的工作区，并 "替代" 软删除期间。 永久工作区删除操作将释放工作区名称，你可以使用相同的名称创建新的工作区。


> [!IMPORTANT]
> 在永久删除工作区时请小心，因为操作是不可逆的，并且工作区及其数据将无法恢复。

当前可以通过 REST API 执行永久工作区删除。

> [!NOTE]
> 任何 API 请求都必须在请求标头中包含持有者授权令牌。
>
> 可以使用获取该令牌：
> - [应用注册](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - 使用开发人员的控制台（F12）在浏览器中导航到 Azure 门户。 在 "**批处理"** 中查找 "**请求标头**" 下的身份验证字符串的实例。 这将采用模式*授权：持有者 <token>* 。 复制此项并将其添加到 API 调用，如示例中所示。
> - 导航到 Azure REST 文档站点。 在任何 API 上按 "**尝试**"，复制持有者令牌，并将其添加到 API 调用。
若要永久删除工作区，请使用[工作区-删除 REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) API 调用和强制标记：
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```

## <a name="recover-workspace"></a>恢复工作区

如果对在软删除操作之前与工作区关联的订阅和资源组具有 "参与者" 权限，则可以在其软删除期间（包括其数据、配置和连接的代理）恢复该工作区。 软删除期结束后，工作区将不可恢复并分配给永久删除。 删除的工作区的名称将在软删除期间保留，并在尝试创建新的工作区时不能使用。  

你可以使用以下工作区 create 方法恢复工作区： [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace)或[REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) ，前提是以下属性使用已删除工作区的详细信息进行填充：

* 订阅 ID
* 资源组名称
* 工作区名称
* 地区

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

恢复操作完成后，工作区及其所有数据都将返回。 删除解决方案和链接服务时，将从工作区中永久删除该解决方案和链接服务，并应重新配置这些服务以使工作区进入其以前配置的状态。 在工作区恢复之后，某些数据可能不能用于查询，直到重新安装关联的解决方案并将其架构添加到工作区。

> [!NOTE]
> * [Azure 门户](https://portal.azure.com)中不支持工作区恢复。 
> * 软删除期间重新创建工作区会显示此工作区名称已被使用。 
> 
