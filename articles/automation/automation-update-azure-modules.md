---
title: 更新 Azure 自动化中的 Azure PowerShell 模块
description: 本文介绍如何更新 Azure 自动化中默认提供的常见 Azure PowerShell 模块。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3f2ceda6e87ce16e910f3d215c1fabe81d522f32
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855559"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>更新 Azure 自动化中的 Azure PowerShell 模块

若要更新你的自动化帐户中的 Azure 模块，你需要使用[更新 azure 模块 runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)，该 runbook 可用作开源。 若要开始使用 **Update-AutomationAzureModulesForAccount** Runbook 来更新 Azure 模块，请从 GitHub 上的[更新 Azure 模块 Runbook 存储库](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)下载该 Runbook。 然后，可以将其导入自动化帐户，或将其作为脚本运行。 若要了解如何导入自动化帐户中的 runbook，请参阅[导入 runbook](manage-runbooks.md#import-a-runbook)。

默认情况下，每个自动化帐户中都提供最常见的 PowerShell 模块。 Azure 团队定期更新 Azure 模块。 因此，若要将自动化帐户中的模块保持为最新状态，应使用[AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook。

由于模块由产品组定期更新，所包含的 cmdlet 可能会发生更改。 这些更改（例如，重命名参数或完全弃用 cmdlet）可能会对 runbook 产生负面影响。

为了避免影响 Runbook 及其自动化过程，请在继续操作之前进行测试和验证。 如果没有用于此目的专用自动化帐户，请考虑创建一个自动化帐户，以便可以在 Runbook 开发期间测试许多不同的方案。 此测试应包括迭代更改，如更新 PowerShell 模块。

如果你在本地开发脚本，则建议在测试时在你的自动化帐户中本地拥有相同的模块版本，以确保收到相同的结果。 在验证结果并且应用了所需的任何更改之后，则可将更改移到生产环境。

> [!NOTE]
> 新自动化帐户可能不包含最新的模块。

> [!NOTE]
> 您将无法删除全局模块，它们是自动化提供的模块。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 

## <a name="considerations"></a>注意事项

以下是使用本文更新 Azure 模块时要考虑的一些注意事项：

* 本文中所述的 runbook 默认支持更新 Azure、AzureRM 和 Az 模块。 若要详细了解如何通过此 runbook 更新 Az 模块，请参阅[更新 Azure 模块 RUNBOOK 自述文件](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md)。 在自动化帐户中使用 Az 模块时，还需要考虑其他一些重要因素。 若要了解详细信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。

* 在启动此 Runbook 之前，请确保为自动化帐户创建了 [Azure 运行方式帐户凭据](manage-runas-account.md)。

* 你可以使用此代码作为常规 PowerShell 脚本而不是 runbook：只需先使用[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) cmdlet 登录到 Azure，然后传递`-Login $false`到脚本。

* 若要在主权云中使用此 Runbook，请使用 `AzEnvironment` 参数将正确的环境传递给 Runbook。  可接受的值为 AzureCloud （Azure 公有云）、AzureChinaCloud、AzureGermanCloud 和 AzureUSGovernment。 可以使用`Get-AzEnvironment | select Name`来检索这些值。 如果不将值传递给此 cmdlet，runbook 默认为 AzureCloud。

* 如果要使用特定 Azure PowerShell 模块版本，而不是 PowerShell 库上提供的最新模块，请将这些版本传递给`ModuleVersionOverrides` **AutomationAzureModulesForAccount** runbook 的可选参数。 有关示例，请参阅 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook。 `ModuleVersionOverrides` 参数中未提及的 Azure PowerShell 模块会更新为 PowerShell 库中的最新模块版本。 如果没有向 `ModuleVersionOverrides` 参数传递任何内容，则所有模块都会更新为 PowerShell 库中的最新模块版本。 此行为与“更新 Azure 模块”按钮的行为相同。****

## <a name="next-steps"></a>后续步骤

访问开源的[更新 Azure 模块 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) 来详细了解该 Runbook。
