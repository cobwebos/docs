---
title: 在 Azure 自动化中排除共享资源的故障
description: 了解如何使用 Azure 自动化共享资源解决问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733574"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>在 Azure 自动化中排除共享资源的故障

本文讨论在 Azure 自动化中使用[共享资源](../automation-intro.md#shared-resources)时可能会遇到的问题的解决方案。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="modules"></a>模块

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>方案：模块在导入过程中卡住

#### <a name="issue"></a>问题

导入或更新 Azure 自动化模块时，模块将卡在"导入"状态。

#### <a name="cause"></a>原因

由于导入 PowerShell 模块是一个复杂的多步骤过程，因此模块可能无法正确导入，并且可能卡处于瞬态状态。 要了解有关导入过程的更多信息，请参阅导入[PowerShell 模块](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解决方法

要解决此问题，必须使用["删除-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) cmdlet"删除卡在"导入"状态中的模块。 然后，可以重试导入模块。

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>方案：AzureRM 模块在导入期间在更新尝试后卡住

#### <a name="issue"></a>问题

尝试更新 AzureRM 模块后，帐户中将保留带有以下消息的横幅：

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

更新自动化帐户中的 AzureRM 模块存在已知问题，该帐户位于以 0 开头的具有数字名称的资源组中。

#### <a name="resolution"></a>解决方法

要更新自动化帐户中的 AzureRM 模块，该帐户必须位于具有字母数字名称的资源组中。 以 0 开头的具有数字名称的资源组此时无法更新 AzureRM 模块。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>场景：模块无法导入，或者 cmdlet 在导入后无法执行

#### <a name="issue"></a>问题

模块无法导入或成功导入，但不会提取任何厘米。

#### <a name="cause"></a>原因

模块无法成功导入到 Azure 自动化中的一些常见原因是：

* 结构与自动化所需的结构不匹配。
* 该模块依赖于其他模块，而后者尚未部署到自动化帐户。
* 该模块的文件夹中缺少依赖项。
* [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) cmdlet 用于上载模块，您尚未提供完整的存储路径，或者未使用可公开访问的 URL 加载模块。

#### <a name="resolution"></a>解决方法

使用这些解决方案中的任何一个来解决此问题。

* 确保模块遵循以下格式：模块Name.zip->模块名称或版本号 ->（模块名称.psm1，模块名称.psd1）。
* 打开 **.psd1**文件，查看模块是否有任何依赖项。 如果有，则将这些模块上传到自动化帐户。
* 确保模块文件夹中存在任何引用的 **.dll**文件。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>方案：更新-AzureModule.ps1 更新模块时挂起

#### <a name="issue"></a>问题

使用[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook 更新 Azure 模块时，模块更新过程将挂起。

#### <a name="cause"></a>原因

使用**Update-AzureModule.ps1**时，用于确定同时更新的模块数的默认设置为 10。 如果同时更新太多模块，则更新过程容易出错。

#### <a name="resolution"></a>解决方法

同一自动化帐户中需要所有 AzureRM 或 Az 模块并不常见。 建议仅导入所需的特定模块。

> [!NOTE]
> 避免导入导入`Az.Automation`所有包含`AzureRM.Automation`模块的整个模块或模块。

如果更新过程挂起，请将`SimultaneousModuleImportJobCount`参数添加到**Update-AzureModule.ps1**脚本，并提供低于默认值 10 的值。 如果实现此逻辑，建议从值 3 或 5 开始。 `SimultaneousModuleImportJobCount`是更新 **-自动化AzureModuleForAccount**系统运行簿的参数，用于更新 Azure 模块。 如果进行此调整，更新过程将运行更长时间，但完成的机会更高。 以下示例显示了该参数及其在 runbook 中的放置位置：

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>运行方式帐户

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>方案：您无法创建或更新"运行即"帐户

#### <a name="issue"></a>问题

在尝试创建或更新运行方式帐户时，接收到类似于以下错误消息的错误：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

没有创建或更新运行方式帐户所需的权限，或者资源在资源组级别锁定。

#### <a name="resolution"></a>解决方法

要创建或更新"运行为"帐户，您必须对"运行为"帐户使用的各种资源具有适当的[权限](../manage-runas-account.md#permissions)。 

如果问题是由于锁，请验证是否可以删除该锁。 然后导航到在 Azure 门户中锁定的资源，右键单击锁，然后单击 **"删除**"。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>方案：在执行 Runbook 时，您会收到错误"无法在 DLL "iplpapi.dll"中找到名为"GetPerAdapterinfo"的入口点"

#### <a name="issue"></a>问题

执行 Runbook 时，您会收到以下异常：

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此错误很可能是由配置不正确的["作为帐户运行"](../manage-runas-account.md)引起的。

#### <a name="resolution"></a>解决方法

确保正确配置了"运行作为"帐户。 然后验证 Runbook 中是否具有使用 Azure 进行身份验证的正确代码。 下面的示例显示了使用"运行作为帐户"在 Runbook 中向 Azure 进行身份验证的代码段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>后续步骤

如果您在上面看不到问题或无法解决问题，请尝试以下渠道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)连接 ，官方 Microsoft Azure 帐户通过将 Azure 社区连接到正确的资源（答案、支持和专家）来改善客户体验。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
