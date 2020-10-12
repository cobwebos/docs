---
title: 在 Azure 自动化中更新 Azure PowerShell 模块
description: 本文介绍如何更新 Azure 自动化中默认提供的常见 Azure PowerShell 模块。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 9cc5e5107784d447640ef0ae77d412885b1e5668
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185766"
---
# <a name="update-azure-powershell-modules"></a>更新 Azure PowerShell 模块

默认情况下，每个自动化帐户中都提供最常见的 PowerShell 模块。 请参阅[默认模块](shared-resources/modules.md#default-modules)。 由于 Azure 团队会定期更新 Azure 模块，因此包含的 cmdlet 可能会发生更改。 这些更改（例如，重命名参数或完全弃用 cmdlet）可能会对 Runbook 产生负面影响。 

> [!NOTE]
> 不能删除全局模块，它们是自动化开箱提供的模块。

## <a name="set-up-an-automation-account"></a>设置自动化帐户

为了避免影响 Runbook 及其自动化过程，请在继续操作之前进行测试和验证。 如果没有用于此目的专用自动化帐户，请考虑创建一个自动化帐户，以便可以在 Runbook 开发期间测试许多不同的方案。 该测试应该包括更新 PowerShell 模块等迭代更改。

请确保为自动化帐户创建了 [Azure 运行方式帐户凭据](manage-runas-account.md)。

如果在本地开发脚本，建议测试时在本地使用与自动化帐户中相同的模块版本，以确保收到相同的结果。 在验证结果并且应用了所需的任何更改之后，则可将更改移到生产环境。

> [!NOTE]
> 新自动化帐户可能不包含最新的模块。

## <a name="obtain-a-runbook-to-use-for-updates"></a>获取用于更新的 Runbook

若要更新自动化帐户中的 Azure 模块，必须使用 [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Runbook，该 Runbook 可用作开源。 若要开始使用此 Runbook 更新 Azure 模块，请从 GitHub 存储库中下载。 然后，可将其导入到自动化帐户，或作为脚本运行。 若要了解如何在自动化帐户中导入 Runbook，请参阅[导入 Runbook](manage-runbooks.md#import-a-runbook)。

默认情况下，Update-AutomationAzureModulesForAccount Runbook 支持更新 Azure、AzureRM 和 Az 模块。 若要详细了解如何通过此 Runbook 更新 Az.Automation 模块，请查看[更新 Azure 模块 Runbook 自述文件](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md)。 在自动化帐户中使用 Az 模块时，还需要考虑其他重要因素。 若要了解更多信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>使用更新 Runbook 代码作为常规 PowerShell 脚本

你可以使用 Runbook 代码作为常规 PowerShell 脚本而非 Runbook。 为此，请先使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) cmdlet 登录到 Azure，然后将 `-Login $false` 传递给脚本。

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>在主权云上使用更新 Runbook

若要在主权云中使用此 Runbook，请使用 `AzEnvironment` 参数将正确的环境传递给 Runbook。 可接受的值为 AzureCloud（Azure 公有云）、AzureChinaCloud、AzureGermanCloud 和 AzureUSGovernment。 可通过使用 `Get-AzEnvironment | select Name` 来检索这些值。 如果不将值传递给此 cmdlet，runbook 默认为 AzureCloud。

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>使用更新 Runbook 更新特定模块版本

若要使用特定的 Azure PowerShell 模块版本而不是 PowerShell 库中的最新可用版本，请将这些版本传递给 Update-AutomationAzureModulesForAccount Runbook 的可选 `ModuleVersionOverrides` 参数。 有关示例，请参阅 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) Runbook。 `ModuleVersionOverrides` 参数中未提及的 Azure PowerShell 模块会更新为 PowerShell 库中的最新模块版本。 如果没有向 `ModuleVersionOverrides` 参数传递任何内容，则所有模块都会更新为 PowerShell 库中的最新模块版本。 此行为与 Azure 门户中的“更新 Azure 模块”按钮的行为相同。

## <a name="next-steps"></a>后续步骤

* 有关使用模块的信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。
* 有关更新 Runbook 的信息，请参阅[更新 Azure 模块 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)。
