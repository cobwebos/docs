---
title: 更新 Azure 自动化中的 Azure 模块
description: 本文介绍如何更新 Azure 自动化中默认提供的常见 Azure PowerShell 模块。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/30/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c09c9df1b8af7adca5c0169cf31881121ba6cc99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427479"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何在 Azure 自动化中更新 Azure PowerShell 模块

您需要使用你的自动化帐户中的 Azure 模块更新[更新 Azure 模块 runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)，这是可用的开放源代码。 若要开始使用 **Update-AutomationAzureModulesForAccount** Runbook 来更新 Azure 模块，请从 GitHub 上的[更新 Azure 模块 Runbook 存储库](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)下载该 Runbook。 然后，可将其导入到自动化帐户，或作为脚本运行。 若要了解如何导入到自动化帐户中的 runbook，请参阅[导入 runbook](manage-runbooks.md#import-a-runbook)。

默认情况下，每个自动化帐户中提供的最常见的 AzureRM PowerShell 模块。 Azure 团队会定期更新 Azure 模块，因此以保持最新你将想要使用[更新 AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook 在自动化帐户中的模块更新。

由于模块由产品组定期更新，所包含的 cmdlet 可能会发生更改。 此操作可能会对 Runbook 产生负面影响，具体要取决于更改类型（如重命名参数或完全弃用 cmdlet）。

为了避免影响 Runbook 及其自动化过程，请在继续操作之前进行测试和验证。 如果没有用于此目的专用自动化帐户，请考虑创建一个自动化帐户，以便可以在 Runbook 开发期间测试许多不同的方案。 该测试应该包括更新 PowerShell 模块等迭代更改。

如果在本地开发脚本，建议测试时在本地使用与自动化帐户中相同的模块版本，以确保收到相同的结果。 在验证结果并且应用了所需的任何更改之后，则可将更改移到生产环境。

> [!NOTE]
> 新自动化帐户可能不包含最新的模块。

## <a name="considerations"></a>注意事项

使用此过程更新 Azure 模块时，需要注意以下一些事项：

* 此 runbook 支持仅更新**Azure**并**AzureRm**当前模块。 [Azure PowerShell Az 模块](/powershell/azure/new-azureps-module-az)支持在自动化帐户中，但不能更新用于此 runbook。 您需要使用时需要考虑的重要因素`Az`模块在自动化帐户中，若要了解详细信息，请参阅[自动化帐户中的使用 Az 模块](az-modules.md)。

* 请避免在包含 Az 模块的自动化帐户中启动此 Runbook。

* 在启动此 Runbook 之前，请确保为自动化帐户创建了 [Azure 运行方式帐户凭据](manage-runas-account.md)。

* 您可以使用此代码作为常规的 PowerShell 脚本而不是 runbook： 只需登录到 Azure 中使用[Connect-azurermaccount](/powershell/module/azurerm.profile/connect-azurermaccount)命令首先，然后传递`-Login $false`的脚本。

* 若要在主权云中使用此 Runbook，请使用 `AzureRmEnvironment` 参数将正确的环境传递给 Runbook。  可接受的值为 **AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud** 和 **AzureUSGovernment**。 可通过使用 `Get-AzureRmEnvironment | select Name` 来检索这些值。 如果没有向此参数传递值，则 Runbook 会默认为 Azure 公有云 **AzureCloud**

* 若要使用特定的 Azure PowerShell 模块版本而不是 PowerShell 库中的最新可用版本，请将这些版本传递给 **Update-AutomationAzureModulesForAccount** Runbook 的 `ModuleVersionOverrides` 可选参数。 有关示例，请参阅 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook。 `ModuleVersionOverrides` 参数中未提及的 Azure PowerShell 模块会更新为 PowerShell 库中的最新模块版本。 如果没有向 `ModuleVersionOverrides` 参数传递任何内容，则所有模块都会更新为 PowerShell 库中的最新模块版本。 此行为与“更新 Azure 模块”按钮的行为相同。 

## <a name="known-issues"></a>已知问题

没有更新 AzureRM 模块是具有数值名以 0 开头的资源组中的自动化帐户中的已知的问题。 若要更新 Azure 模块在自动化帐户中，它必须是字母数字名称的资源组中。 具有从 0 开始的数字名称的资源组不能在这一次更新 AzureRM 模块。

## <a name="next-steps"></a>后续步骤

访问开源的[更新 Azure 模块 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) 来详细了解该 Runbook。
