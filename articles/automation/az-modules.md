---
title: Azure 自动化中的 Az 模块支持
description: 本文提供有关在 Azure 自动化中使用 Az 模块的信息。
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548358"
---
# <a name="az-module-support-in-azure-automation"></a>Azure 自动化中的 Az 模块支持

Azure 自动化支持在 Runbook 中使用[Azure PowerShell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。 任何新或现有自动化帐户中不会自动导入 Az 模块。 

## <a name="considerations"></a>注意事项

在 Azure 自动化中使用 Az 模块时，需要考虑很多事项。 自动化帐户中的更高级别的解决方案可以使用 Runbook 和模块。 编辑 runbook 或升级模块可能会导致 runbook 出现问题。 在导入新`Az`模块之前，应在单独的自动化帐户中仔细测试所有 Runbook 和解决方案。 对模块的任何修改都会对[开始/停止](automation-solution-vm-management.md)解决方案产生负面影响。 我们不建议在包含任何解决方案的自动化帐户中更改模块和 Runbook。 此行为并非特定于 Az 模块。 在向自动化帐户引入任何更改时，应考虑此行为。

在自动化`Az`帐户中导入模块不会自动导入 Runbook 使用的 PowerShell 会话中的模块。 在以下情况中，模块会导入到 PowerShell 会话中：

* 从 runbook 调用模块的 cmdlet 时
* Runbook 使用 `Import-Module` cmdlet 显式导入模块时
* 依赖于该模块的其他模块被导入到 PowerShell 会话中时

> [!IMPORTANT]
> 请务必确保自动化帐户中的 Runbook 仅导入或`Az``AzureRM`模块到 Runbook 使用的 PowerShell 会话中，而不是同时导入两者。 如果在`Az`Runbook`AzureRM`中之前导入，则 Runbook 会完成，但引用[Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet 的错误显示在作业流中，并且 cmdlet 可能无法正确执行。 如果导入`AzureRM`然后`Az`，Runbook 仍然完成，但在作业流中收到错误，指出两者`Az``AzureRM`不能在同一会话中导入，也不能在同一 Runbook 中使用。

## <a name="migrating-to-az-modules"></a>迁移到 Az 模块

建议您在测试自动化帐户中测试迁移到 Az 模块。 创建自动化帐户后，可以使用本节中的说明处理模块。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>停止并取消计划使用 AzureRM cmdlet 的所有 Runbook

若要确保不运行使用 `AzureRM` cmdlet 的任何现有 runbook，应停止并取消计划使用 `AzureRM` 模块的所有 runbook。 通过运行类似于此示例的代码，您可以看到存在哪些计划以及必须删除哪些计划。

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

请务必分别查看每个计划，以确保在必要时可以重新计划运行簿。

### <a name="import-the-az-modules"></a>导入 Az 模块

仅导入 runbook 所需的 Az 模块。 不要导入汇总`Az`模块，因为它包含所有`Az.*`模块。 此指南适用于所有模块。

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 模块是其他 `Az.*` 模块的依赖项。 因此，在导入任何其他模块之前，需要将此模块导入自动化帐户。

从您的自动化帐户中，选择**共享资源**下的**模块**。 单击“浏览库”，打开“浏览库”页********。  在搜索栏中，输入模块名称（例如 `Az.Accounts`）。 在 PowerShell 模块页面上，单击"**导入**"以将模块导入自动化帐户。

![从自动化帐户导入模块](media/az-modules/import-module.png)

此导入过程也可以通过[PowerShell 库](https://www.powershellgallery.com)通过搜索模块进行导入来完成。 找到模块后，选择该模块，然后在“Azure 自动化”选项卡下，单击“部署到 Azure 自动化”********。

![直接从库中导入模块](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>测试 Runbook

将`Az`模块导入自动化帐户后，可以开始编辑 Runbook 以使用 Az 模块。 大多数 cmdlet 具有相同的名称，`AzureRM`但已更改为`Az`的除外。 有关不遵循此命名约定的模块列表，请参阅[异常列表](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

测试 Runbook 的修改以使用新 cmdlet 的一种方法是在 Runbook 的开头使用`Enable-AzureRMAlias -Scope Process`。 通过将此命令添加到 runbook，脚本可以运行而不进行更改。

## <a name="after-migration-details"></a>迁移后详细信息

迁移完成后，不要尝试使用自动化帐户上的`AzureRM`模块开始运行簿。 还建议不要导入或更新`AzureRM`帐户上的模块。 请考虑迁移到`Az`的帐户，并且仅使用`Az`模块操作。 

创建新的自动化帐户时，仍安装现有`AzureRM`模块。 您仍可以使用`AzureRM`cmdlet 更新教程运行簿。 不应运行这些 Runbook。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Az 模块，请参阅 [Az 模块入门](/powershell/azure/get-started-azureps?view=azps-1.1.0)。
