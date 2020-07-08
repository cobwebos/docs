---
title: 删除和恢复 Azure Log Analytics 工作区 | Microsoft Docs
description: 了解在个人订阅中创建 Log Analytics 工作区后如何删除它，以及如何重构工作区模型。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: c93ba19cc70aa6b5df054dcc2e7e06885b02d661
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367948"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>删除和恢复 Azure Log Analytics 工作区

本文介绍了 Azure Log Analytics 工作区软删除的概念，以及如何恢复已删除的工作区。

## <a name="considerations-when-deleting-a-workspace"></a>删除工作区时的注意事项

删除 Log Analytics 工作区时，将执行软删除操作，在 14 天内允许恢复工作区（包括其数据和连接的代理），无论是意外删除还是有意删除。 软删除期之后，工作区资源及其数据将不可恢复 - 其数据将排队等待永久删除，并在 30 天内彻底清除。 工作区名称为“已发布”，你可以使用它来创建新的工作区。

> [!NOTE]
> 如果要替代软删除行为，并永久删除工作区，请执行[永久工作区删除](#permanent-workspace-delete)中的步骤。

删除工作区时需谨慎，因为其中可能会含有对服务操作产生不利影响的重要数据和配置。 查看哪些代理、解决方案和其他 Azure 服务将其数据存储在 Log Analytics 中，例如：

* 管理解决方案
* Azure 自动化
* 在 Windows 和 Linux 虚拟机上运行的代理
* 在环境中于 Windows 和 Linux 计算机上运行的代理
* System Center Operations Manager

软删除操作会删除工作区资源，并且任何关联用户的权限都将中断。 如果这些用户与其他工作区关联，他们可以继续通过其他工作区使用 Log Analytics。

## <a name="soft-delete-behavior"></a>软删除行为

工作区删除操作会删除工作区资源管理器资源，但其配置和数据将保留 14 天，同时会显示工作区已删除。 在软删除期间，被配置为向工作区报告的任何代理和 System Center Operations Manager 管理组继续处于孤立状态。 该服务还提供用于恢复已删除工作区（包括其数据和已连接的资源）的机制，从实质上撤消删除。

> [!NOTE] 
> 已安装的解决方案和链接服务（例如 Azure 自动化帐户）在删除时将从工作区中永久删除，并且无法恢复。 应在恢复操作后进行重新配置，使工作区恢复到其以前配置的状态。

可以使用 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0)、[REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) 或 [Azure 门户](https://portal.azure.com)删除工作区。

### <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。
3. 在 Log Analytics 工作区的列表中，选择一个工作区，然后从中间窗格的顶端单击“删除”。
4. 确认页面随即出现，显示过去一周内工作区的数据引入。 键入要确认的工作区的名称，然后单击“删除”。

   ![确认删除工作区](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>永久删除工作区
软删除方法可能不适用于开发和测试等情况，在这些情况下需要使用相同的设置和工作区名称重复部署。 在这些情况下，可以永久删除工作区并“替换”软删除期。 永久删除工作区操作将释放工作区名称，你可以使用相同的名称创建新的工作区。


> [!IMPORTANT]
> 请谨慎使用永久删除工作区操作，因为这是不可逆的，并且无法恢复工作区及其数据。

添加 "-force" 标记以永久删除你的工作区：

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -Force
```

## <a name="recover-workspace"></a>恢复工作区
当意外或有意删除 Log Analytics 工作区时，该服务会将工作区置于软删除状态，从而使其无法被任何操作访问。 已删除工作区的名称将在软删除期间保留，并且不能用于创建新工作区。 软删除期之后，工作区将不可恢复，会计划将其永久删除，并且发布的名称可用于创建新工作区。

你可以在软删除期间恢复工作区，包括其数据、配置和已连接的代理。 在执行软删除操作之前，你需要对工作区所在的订阅和资源组具有“参与者”权限。 工作区恢复通过创建包含已删除工作区的详细信息的 Log Analytics 工作区来执行，其中包括：

- 订阅 ID
- 资源组名称
- 工作区名称
- 区域

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。 你将看到所选范围内的工作区列表。
3. 单击左上侧菜单中的“恢复”，打开包含处于软删除状态且可恢复的工作区页面。

   ![恢复工作区](media/delete-workspace/recover-menu.png)

4. 选择工作区，然后单击“恢复”以恢复该工作区。

   ![恢复工作区](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

恢复操作完成后，将恢复工作区及其所有数据。 解决方案和链接服务在删除时已从工作区中永久删除，应进行重新配置，使工作区恢复到其以前配置的状态。 工作区恢复之后，在重新安装关联的解决方案并将其架构添加到工作区之前，某些数据可能无法进行查询。

> [!NOTE]
> * 在软删除期间重新创建一个工作区，会显示此工作区名称已在使用中。 
 
## <a name="troubleshooting"></a>疑难解答

必须至少拥有“Log Analytics 参与者”权限才能删除工作区。

* 如果不确定删除的工作区是否处于软删除状态且可以恢复，请单击 " *Log Analytics 工作区*中的[恢复](#recover-workspace)" 页，查看每个订阅的软删除工作区的列表。 永久删除的工作区不包括在列表中。
* 如果在创建工作区时收到一条错误消息“此工作区名称已在使用中”或“发生冲突”，则可能是由于以下原因 ：
  * 工作区名称不可用，并且已被组织中的某个用户或其他客户使用。
  * 该工作区是在最近 14 天内删除的，其名称仍处于软删除期间保留状态。 若要替换软删除，同时永久删除你的工作区并创建新的同名工作区，请按照以下步骤，先恢复工作区再执行永久删除：<br>
     1. [恢复](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace)工作区。
     2. [永久删除](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete)工作区。
     3. 使用相同的工作区名称创建新的工作区。
