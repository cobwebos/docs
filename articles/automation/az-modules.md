---
title: Azure 自动化中的 Az 模块支持
description: 本文提供有关在 Azure 自动化中使用 Az 模块的信息。
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: a8d6d25a2ba7f0040b13982f14f3d6081ac32f15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637994"
---
# <a name="az-module-support-in-azure-automation"></a>Azure 自动化中的 Az 模块支持

Azure 自动化支持在 Runbook 中使用[Azure PowerShell Az 模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。 汇总 Az 模块不会在任何新的或现有的自动化帐户中自动导入。 

## <a name="considerations"></a>注意事项

在 Azure 自动化中使用 Az 模块时，需要考虑以下几点：

* 自动化帐户中的更高级别解决方案可以使用 Runbook 和模块。 因此，编辑 Runbook 或升级模块可能会导致解决方案出现问题。 在导入新的 Az 模块之前，应在单独的自动化帐户中仔细测试所有 Runbook 和解决方案。 

* 对模块的任何修改都会对[开始/停止](automation-solution-vm-management.md)解决方案产生负面影响。 

* 在自动化帐户中导入 Az 模块不会自动在 Runbook 使用的 PowerShell 会话中导入该模块。 在以下情况中，模块会导入到 PowerShell 会话中：

    * 当 Runbook 从模块调用 cmdlet 时
    * 当 Runbook 使用`Import-Module`cmdlet 显式导入模块时
    * 当 Runbook 导入另一个模块时，具体取决于模块

> [!NOTE]
> 我们不建议在包含任何解决方案的自动化帐户中更改模块和 Runbook。 此规定并非特定于 Az 模块。 在向自动化帐户引入任何更改时，应考虑这一点。

> [!IMPORTANT]
> 确保自动化帐户中的 Runbook 将 Az 模块或[AzureRM](https://www.powershellgallery.com/packages/AzureRM/6.13.1)模块（但不是两者）导入 PowerShell 会话。 如果 Runbook 在 AzureRM 模块之前导入 Az 模块，则 Runbook 将完成。 但是，引用[Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet 的错误显示在作业流中，cmdlet 可能无法正确执行。 如果 Runbook 在 Az 模块之前导入 AzureRM 模块，则 Runbook 也会完成。 但是，在这种情况下，您在作业流中收到一个错误，指出无法在同一会话中导入 Az 和 AzureRM 或在同一 Runbook 中使用。

## <a name="migrating-to-az-modules"></a>迁移到 Az 模块

我们建议您在测试自动化帐户中测试迁移到 Az 模块。 创建帐户后，可以使用本节中的说明处理模块。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止并取消计划使用 AzureRM 模块的所有 Runbook

为了确保不运行任何使用 AzureRM 模块的现有 Runbook，停止并取消计划所有受影响的 Runbook。 通过运行类似于此示例的代码，您可以看到存在哪些计划以及要删除的计划：

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

请务必分别查看每个计划，以确保如有必要，可以在未来为 Runbook 重新计划。

### <a name="import-the-az-modules"></a>导入 Az 模块

>[!NOTE]
>仅导入 Runbook 需要的 Az 模块。 不要导入汇总 Az 模块，因为它包含所有 Az 模块。 此指南适用于所有模块。

[Az.帐户](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)模块是其他 Az 模块的依赖项。 因此，Runbook 必须在导入任何其他模块之前将此模块导入自动化帐户。

要导入 Azure 门户中的模块，我们：

1. 从您的自动化帐户中，选择**共享资源**下的**模块**。 
2. 单击“浏览库”，打开“浏览库”页****。  
3. 在搜索栏中，输入模块名称，例如 。 `Az.Accounts` 
4. 在 PowerShell 模块页面上，单击"**导入**"以将模块导入自动化帐户。

![从自动化帐户导入模块](media/az-modules/import-module.png)

此导入过程也可以通过[PowerShell 库](https://www.powershellgallery.com)通过搜索模块进行导入来完成。 找到模块后，选择它，选择 Azure**自动化**选项卡，然后单击"**部署到 Azure 自动化**"。

![直接从库中导入模块](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>测试 Runbook

将 Az 模块导入自动化帐户后，可以开始编辑 Runbook 以使用这些模块。 大多数 cmdlet 的名称与 AzureRM 模块的名称相同，但 AzureRM（或 AzureRm） 前缀已更改为 Az。 有关不遵循此命名约定的模块列表，请参阅[异常列表](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

测试 Runbook 的修改以使用新 cmdlet 的一种方法是在 Runbook 的开头使用`Enable-AzureRmAlias -Scope Process`。 通过将此命令添加到 runbook，脚本可以运行而不进行更改。

## <a name="after-migration-details"></a>迁移后详细信息

迁移完成后，不要尝试使用自动化帐户上的 AzureRM 模块开始运行簿。 还建议不要导入或更新帐户上的 AzureRM 模块。 请考虑迁移到 Az 的帐户，并且仅使用 Az 模块操作。 

创建新的自动化帐户时，仍将安装现有的 AzureRM 模块。 您仍可以使用 AzureRM cmdlet 更新教程运行簿。 但是，不应运行这些 Runbook。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Az 模块，请参阅 [Az 模块入门](/powershell/azure/get-started-azureps?view=azps-1.1.0)。
