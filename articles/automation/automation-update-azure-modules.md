---
title: 更新 Azure 自动化中的 Azure 模块
description: 本文介绍如何更新 Azure 自动化中默认提供的常见 Azure PowerShell 模块。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d8f57310cf4dbc2a27761fc44cfde6c8fd2791a2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005533"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何在 Azure 自动化中更新 Azure PowerShell 模块

若要更新自动化帐户中的 Azure 模块，建议使用开源的[更新 Azure 模块 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)。 此外，仍可以在门户中使用“更新 Azure 模块”按钮来更新 Azure 模块。 若要详细了解如何使用开源 Runbook，请参阅[使用开源 Runbook 更新 Azure 模块](#open-source)。

默认情况下，每个自动化帐户中都提供最常见的 Azure PowerShell 模块。 Azure 团队定期更新 Azure 模块。 在自动化帐户中，我们提供了一种方法，用于在门户中有新版本时更新帐户中的模块。

由于模块由产品组定期更新，所包含的 cmdlet 可能会发生更改。 此操作可能会对 Runbook 产生负面影响，具体要取决于更改类型（如重命名参数或完全弃用 cmdlet）。

为了避免影响 Runbook 及其自动化过程，请在继续操作之前进行测试和验证。 如果没有用于此目的专用自动化帐户，请考虑创建一个自动化帐户，以便可以在 Runbook 开发期间测试许多不同的方案。 该测试应该包括更新 PowerShell 模块等迭代更改。 

如果在本地开发脚本，建议测试时在本地使用与自动化帐户中相同的模块版本，以确保收到相同的结果。 在验证结果并且应用了所需的任何更改之后，则可将更改移到生产环境。

> [!NOTE]
> 新自动化帐户可能不包含最新的模块。

## <a name="open-source"></a>使用开源 Runbook 更新 Azure 模块

若要开始使用 **Update-AutomationAzureModulesForAccount** Runbook 来更新 Azure 模块，请从 GitHub 上的[更新 Azure 模块 Runbook 存储库](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)下载该 Runbook。 然后，可将其导入到自动化帐户，或作为脚本运行。 [更新 Azure 模块 Runbook 存储库](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)中提供了有关此操作的说明。

### <a name="considerations"></a>注意事项

使用此过程更新 Azure 模块时，需要注意以下一些事项：

* 如果使用原始名称 `Update-AutomationAzureModulesForAccount` 导入此 Runbook，它将替代同名的内部 Runbook。 因此，按下“更新 Azure 模块”按钮，或者直接通过此自动化帐户的 Azure 资源管理器 API 调用导入的 Runbook 时，此 Runbook 将会运行。

* 目前仅支持 `Azure` 和 `AzureRM.*` 模块。 新的 [Azure PowerShell Az 模块](/powershell/azure/new-azureps-module-az)目前尚不受支持。

* 请避免在包含 Az 模块的自动化帐户中启动此 Runbook。

* 在启动此 Runbook 之前，请确保为自动化帐户创建了 [Azure 运行方式帐户凭据](manage-runas-account.md)。

* 可以使用此代码作为常规 PowerShell 脚本而不是 Runbook：只需先使用 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) 命令登录到 Azure，然后将 `-Login $false` 传递给脚本即可。

* 若要在主权云中使用此 Runbook，请使用 `AzureRmEnvironment` 参数将正确的环境传递给 Runbook。  可接受的值为 **AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud** 和 **AzureUSGovernment**。 可通过使用 `Get-AzureRmEnvironment | select Name` 来检索这些值。 如果没有向此参数传递值，则 Runbook 会默认为 Azure 公有云 **AzureCloud**

* 若要使用特定的 Azure PowerShell 模块版本而不是 PowerShell 库中的最新可用版本，请将这些版本传递给 **Update-AutomationAzureModulesForAccount** Runbook 的 `ModuleVersionOverrides` 可选参数。 有关示例，请参阅 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook。 `ModuleVersionOverrides` 参数中未提及的 Azure PowerShell 模块会更新为 PowerShell 库中的最新模块版本。 如果没有向 `ModuleVersionOverrides` 参数传递任何内容，则所有模块都会更新为 PowerShell 库中的最新模块版本。 此行为与“更新 Azure 模块”按钮的行为相同。

## <a name="update-azure-modules-in-the-azure-portal"></a>在 Azure 门户中更新 Azure 模块

1. 在自动化帐户的“模块”页中有一个名为“更新 Azure 模块”的选项。 该选项始终处于启用状态。<br><br> ![“模块”页中的“更新 Azure 模块”选项](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > 在更新 Azure 模块之前，建议在一个测试自动化帐户中更新它们，以确保现有脚本按预期工作。
  >
  > “更新 Azure 模块”按钮仅在公有云中可用。 它在[主权区域](https://azure.microsoft.com/global-infrastructure/)中不可用。 请使用 **Update-AutomationAzureModulesForAccount** Runbook 来更新 Azure 模块。 可以从[更新 Azure 模块 Runbook 存储库](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)下载该 Runbook。 若要详细了解如何使用开源 Runbook，请参阅[使用开源 Runbook 更新 Azure 模块](#open-source)。

2. 单击“更新 Azure 模块”，会出现一条询问是否要继续的确认通知。<br><br> ![更新 Azure 模块通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. 单击“是”，模块更新过程将开始进行。 更新过程大约需要 15-20 分钟来更新以下模块：

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    如果模块已经是最新的，则该过程只需几秒钟即可完成。 更新过程完成后会收到通知。<br><br> ![更新 Azure 模块更新状态](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    .NET core AzureRm 模块 (AzureRm.*.Core) 在 Azure 自动化中不受支持，并且无法导入。

> [!NOTE]
> 当运行新的计划作业时，Azure 自动化将在自动化帐户中使用最新模块。  

如果在 Runbook 中使用这些 Azure PowerShell 模块中的 cmdlet，需要大约每月运行一次此更新过程，以确保拥有最新的模块。 更新模块时，Azure 自动化使用 `AzureRunAsConnection` 连接进行身份验证。 如果服务主体已过期或不再以订阅级别存在，模块更新会失败。

## <a name="next-steps"></a>后续步骤

访问开源的[更新 Azure 模块 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) 来详细了解该 Runbook。
