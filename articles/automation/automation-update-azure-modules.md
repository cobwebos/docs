---
title: 更新 Azure 自动化中的 Azure 模块
description: 本文介绍如何更新 Azure 自动化中默认提供的常见 Azure PowerShell 模块。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 721bb3579fec10df88be471d67f68c0846aa9432
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632112"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何在 Azure 自动化中更新 Azure PowerShell 模块

要更新自动化帐户中的 Azure 模块，需要使用可作为开源的["更新 Azure 模块 runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)"。 若要开始使用 **Update-AutomationAzureModulesForAccount** Runbook 来更新 Azure 模块，请从 GitHub 上的[更新 Azure 模块 Runbook 存储库](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)下载该 Runbook。 然后，可将其导入到自动化帐户，或作为脚本运行。 要了解如何在自动化帐户中导入 Runbook，请参阅导入[Runbook](manage-runbooks.md#importing-a-runbook)。

默认情况下，每个自动化帐户中都会提供最常见的 AzureRM PowerShell 模块。 Azure 团队定期更新 Azure 模块，因此要保持最新状态，您需要使用[更新-自动化 AzureModuleForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Runbook 更新自动化帐户中的模块。

由于模块由产品组定期更新，所包含的 cmdlet 可能会发生更改。 此操作可能会对 Runbook 产生负面影响，具体要取决于更改类型（如重命名参数或完全弃用 cmdlet）。

为了避免影响 Runbook 及其自动化过程，请在继续操作之前进行测试和验证。 如果没有用于此目的专用自动化帐户，请考虑创建一个自动化帐户，以便可以在 Runbook 开发期间测试许多不同的方案。 该测试应该包括更新 PowerShell 模块等迭代更改。

如果在本地开发脚本，建议测试时在本地使用与自动化帐户中相同的模块版本，以确保收到相同的结果。 在验证结果并且应用了所需的任何更改之后，则可将更改移到生产环境。

> [!NOTE]
> 新自动化帐户可能不包含最新的模块。

> [!NOTE]
> 您将无法删除全局模块 - 自动化提供的模块开箱即用。

## <a name="considerations"></a>注意事项

使用此过程更新 Azure 模块时，需要注意以下一些事项：

* 默认情况下，此 Runbook 支持更新**Azure**和**AzureRm**模块。 此 Runbook 还支持更新**Az**模块。 有关使用此 Runbook 更新`Az`模块的详细信息，请查看更新 Azure[模块运行簿 README。](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) 在使用自动化帐户中的`Az`模块时，您需要考虑其他重要因素，以了解更多信息，请参阅[在自动化帐户中使用 Az 模块](az-modules.md)。

* 在启动此 Runbook 之前，请确保为自动化帐户创建了 [Azure 运行方式帐户凭据](manage-runas-account.md)。

* 您可以将此代码用作常规 PowerShell 脚本，而不是 Runbook：只需先使用[Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount)命令登录到 Azure，然后传递到`-Login $false`脚本。

* 若要在主权云中使用此 Runbook，请使用 `AzureRmEnvironment` 参数将正确的环境传递给 Runbook。  可接受的值为 **AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud** 和 **AzureUSGovernment**。 可通过使用 `Get-AzureRmEnvironment | select Name` 来检索这些值。 如果没有向此参数传递值，则 Runbook 会默认为 Azure 公有云 **AzureCloud**

* 若要使用特定的 Azure PowerShell 模块版本而不是 PowerShell 库中的最新可用版本，请将这些版本传递给 **Update-AutomationAzureModulesForAccount** Runbook 的 `ModuleVersionOverrides` 可选参数。 有关示例，请参阅 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook。 `ModuleVersionOverrides` 参数中未提及的 Azure PowerShell 模块会更新为 PowerShell 库中的最新模块版本。 如果没有向 `ModuleVersionOverrides` 参数传递任何内容，则所有模块都会更新为 PowerShell 库中的最新模块版本。 此行为与“更新 Azure 模块”按钮的行为相同。****

## <a name="next-steps"></a>后续步骤

访问开源的[更新 Azure 模块 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) 来详细了解该 Runbook。
