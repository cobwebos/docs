---
title: 在 Azure 自动化中使用 Az 模块
description: 本文提供有关在 Azure 自动化中使用 Az 模块的信息
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c3f46e40dfaf0d1ba2ab393b593cdd479c48c45d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585056"
---
# <a name="az-module-support-in-azure-automation"></a>Azure 自动化中的 Az 模块支持

Azure 自动化支持在 runbook 中使用 [Azure Powershell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)的功能。 Az 模块不会自动导入任何新的或现有的自动化帐户。 本文讨论如何将 Az 模块与 Azure 自动化配合使用。

## <a name="considerations"></a>注意事项

在 Azure 自动化中使用 Az 模块时，需要考虑很多事项。 自动化帐户中更高级别的解决方案可以使用 runbook 和模块。 编辑 runbook 或升级模块可能会导致 runbook 出现问题。 在导入新的 `Az` 模块前，应该在单独的自动化帐户中认真测试所有 runbook 和解决方案。 对模块的任何修改都会对更高级别的解决方案产生负面影响，例如更新管理以及在非工作时间启动/停止 VM。 建议不要更改包含任何解决方案的自动化帐户中的模块和 runbook。 此行为并非特定于 Az 模块。 在自动化帐户中引入任何更改时，都应考虑此行为。

在自动化帐户中导入 `Az` 模块的行为不会在 runbook 使用的 PowerShell 会话中自动导入该模块。 在以下情况中，模块会导入到 PowerShell 会话中：

* 从 runbook 调用模块的 cmdlet 时
* Runbook 使用 `Import-Module` cmdlet 显式导入模块时
* 依赖于该模块的其他模块被导入到 PowerShell 会话中时

> [!IMPORTANT]
> 请务必确保自动化帐户中的 runbook 仅将 `Az` 或 `AzureRM` 模块导入到 runbook 使用的 PowerShell 会话中，而不是同时导入两者。 如果 `Az` 在 `AzureRM` 之前导入到 runbook 中，则 runbook 将完成，但作业流中会显示[引用 get_SerializationSettings 方法出错](troubleshoot/runbooks.md#get-serializationsettings)，并且 cmdlet 可能不会正确执行。 如果先导入 `AzureRM`，然后导入 `Az`，则 runbook 仍将完成，但会在作业流中看到错误，指出无法在同一会话中导入或无法在同一 runbook 中使用 `Az` 和 `AzureRM`。

## <a name="migrating-to-az-modules"></a>迁移到 Az 模块

建议在测试自动化帐户中使用 Az 模块（而不是 AzureRM 模块）来测试迁移。 创建该自动化帐户后，即可使用以下步骤确保迁移顺利进行：

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>停止并取消计划使用 AzureRM 模块的所有 runbook

若要确保不运行使用 `AzureRM` cmdlet 的任何现有 runbook，应停止并取消计划使用 `AzureRM` 模块的所有 runbook。 可以通过运行以下示例来查看存在哪些计划以及必须删除哪些计划：

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

请务必分别查看每个计划，以确保未来可在必要时为 runbook 重新进行计划。

### <a name="import-the-az-modules"></a>导入 Az 模块

仅导入 runbook 所需的 Az 模块。 不要导入汇总 `Az` 模块，因为它包含要导入的所有 `Az.*` 模块。 此指南适用于所有模块。

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 模块是其他 `Az.*` 模块的依赖项。 因此，需要先将此模块导入自动化帐户，然后才能导入任何其他模块。

在自动化帐户中，选择“共享资源”下的“模块”。 单击“浏览库”，打开“浏览库”页。  在搜索栏中，输入模块名称（例如 `Az.Accounts`）。 在 PowerShell 模块页，单击“导入”，将模块导入自动化帐户。

![从自动化帐户导入模块](media/az-modules/import-module.png)

还可以通过搜索模块，使用 [PowerShell 库](https://www.powershellgallery.com)完成此导入过程。 找到模块后，选择该模块，然后在“Azure 自动化”选项卡下，单击“部署到 Azure 自动化”。

![直接从库中导入模块](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>测试 runbook

在自动化帐户中导入 `Az` 模块后，现在可以开始编辑 runbook，以改为使用 Az 模块。 除 `AzureRM` 已更改为 `Az` 外，大多数 cmdlet 的名称不变。 有关不遵循此过程的模块的列表，请参阅[例外列表](/powershell/azure/migrate-from-azurerm-to-az?view=azps-1.1.0#change-module-imports-and-cmdlet-names)。

在修改 runbook 以使用新的 cmdlet 前测试 runbook 的一种方法是，在 runbook 的开头使用 `Enable-AzureRMAlias -Scope Process`。 通过将其添加到 runbook，runbook 无需更改即可运行。

## <a name="after-migration-details"></a>迁移后详细信息

迁移完成后，不要再使用帐户上的 `AzureRM` 模块启动 runbook。 还建议不要在此帐户上导入或更新 `AzureRM` 模块。 从这一刻开始，请记住此帐户已迁移到 `Az`，并且仅使用 `Az` 模块运行。 创建新的自动化帐户时，仍将安装现有的 `AzureRM` 模块，并且仍将使用 `AzureRM` cmdlet 创作教程 runbook。 不应运行这些 runbook。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Az 模块，请参阅 [Az 模块入门](/powershell/azure/get-started-azureps?view=azps-1.1.0)。