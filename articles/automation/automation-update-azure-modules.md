---
title: 更新 Azure 自动化中的 Azure 电源外壳模块
description: 本文介绍如何更新 Azure 自动化中默认提供的常见 Azure PowerShell 模块。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769665"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>更新 Azure 自动化中的 Azure 电源外壳模块

要更新自动化帐户中的 Azure 模块，需要使用可作为开源的["更新 Azure 模块 runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)"。 若要开始使用 **Update-AutomationAzureModulesForAccount** Runbook 来更新 Azure 模块，请从 GitHub 上的[更新 Azure 模块 Runbook 存储库](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)下载该 Runbook。 然后，您可以将它导入自动化帐户，或将其作为脚本运行。 要了解如何在自动化帐户中导入 Runbook，请参阅导入[Runbook](manage-runbooks.md#importing-a-runbook)。

默认情况下，每个自动化帐户中都会提供最常见的 PowerShell 模块。 Azure 团队定期更新 Azure 模块。 因此，要使自动化帐户中的模块保持最新，应使用[更新自动化 AzureModuleFor帐户](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)运行簿。

由于模块由产品组定期更新，所包含的 cmdlet 可能会发生更改。 例如，这些更改重命名参数或完全弃用 cmdlet 可能会对 Runbook 产生负面影响。

为了避免影响 Runbook 及其自动化过程，请在继续操作之前进行测试和验证。 如果没有用于此目的专用自动化帐户，请考虑创建一个自动化帐户，以便可以在 Runbook 开发期间测试许多不同的方案。 此测试应包括迭代更改，例如更新 PowerShell 模块。

如果在本地开发脚本，建议在测试时在本地具有与自动化帐户相同的模块版本，以确保收到相同的结果。 在验证结果并且应用了所需的任何更改之后，则可将更改移到生产环境。

> [!NOTE]
> 新自动化帐户可能不包含最新的模块。

> [!NOTE]
> 您将无法删除全局模块，这些模块是自动化提供的开箱即用的模块。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 

## <a name="considerations"></a>注意事项

以下是使用本文更新 Azure 模块时需要考虑的一些注意事项：

* 本文中描述的 Runbook 默认支持更新 Azure、AzureRM 和 Az 模块。 有关使用此 Runbook 更新 Az.自动化模块的详细信息，请查看[更新 Azure 模块 RUNbook README。](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) 在使用自动化帐户中的 Az 模块时，需要考虑其他重要因素。 要了解更多信息，请参阅[在 Azure 自动化 中管理模块](shared-resources/modules.md)。

* 在启动此 Runbook 之前，请确保为自动化帐户创建了 [Azure 运行方式帐户凭据](manage-runas-account.md)。

* 您可以将此代码用作常规 PowerShell 脚本，而不是 Runbook：只需先使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) cmdlet 登录到 Azure，然后传递到`-Login $false`脚本。

* 若要在主权云中使用此 Runbook，请使用 `AzEnvironment` 参数将正确的环境传递给 Runbook。  可接受的值包括 AzureCloud（Azure 公共云）、Azure 中国云、Azure 德语云和 AzureUS 政府。 可以使用 检索这些值`Get-AzEnvironment | select Name`。 如果不将值传递给此 cmdlet，则 Runbook 默认为 AzureCloud。

* 如果要使用特定的 Azure PowerShell 模块版本，而不是 PowerShell 库中提供的最新模块，请将这些版本传递给`ModuleVersionOverrides`**更新-自动化AzureModuleFor帐户**手册的可选参数。 有关示例，请参阅 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook。 `ModuleVersionOverrides` 参数中未提及的 Azure PowerShell 模块会更新为 PowerShell 库中的最新模块版本。 如果没有向 `ModuleVersionOverrides` 参数传递任何内容，则所有模块都会更新为 PowerShell 库中的最新模块版本。 此行为与“更新 Azure 模块”按钮的行为相同。****

## <a name="next-steps"></a>后续步骤

访问开源的[更新 Azure 模块 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) 来详细了解该 Runbook。
