---
title: 排查 Azure 自动化中的共享资源问题
description: 了解如何排查和解决 Azure 自动化共享资源的问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733574"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>排查 Azure 自动化中的共享资源问题

本文介绍使用 Azure 自动化中的[共享资源](../automation-intro.md#shared-resources)时可能会遇到的问题的解决方案。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="modules"></a>模块

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>场景：导入过程中模块停滞

#### <a name="issue"></a>问题

导入或更新 Azure 自动化模块时，模块会停滞在导入状态。

#### <a name="cause"></a>原因

由于导入 PowerShell 模块是一个复杂的多步骤过程，因此模块可能无法正确导入，并且可能会停滞为暂时性状态。 若要了解有关导入过程的详细信息，请参阅[导入 PowerShell 模块](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解决方法

若要解决此问题，必须使用[AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) cmdlet 删除停滞处于导入状态的模块。 然后，可以重试导入模块。

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>情况： AzureRM 模块在更新尝试后会停滞在导入过程中

#### <a name="issue"></a>问题

尝试更新 AzureRM 模块后，会在你的帐户中保留一条包含以下消息的横幅：

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

如果某个资源组中的 AzureRM 模块的名称以0开头，则会出现一个已知问题。

#### <a name="resolution"></a>解决方法

若要在自动化帐户中更新 AzureRM 模块，帐户必须位于带有字母数字名称的资源组中。 名称以0开头的资源组目前无法更新 AzureRM 模块。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>场景：模块无法导入，或者 cmdlet 在导入后无法执行

#### <a name="issue"></a>问题

模块无法导入或导入成功，但未提取 cmdlet。

#### <a name="cause"></a>原因

模块无法成功导入到 Azure 自动化中的一些常见原因是：

* 结构与自动化所需的结构不匹配。
* 该模块依赖于其他模块，而后者尚未部署到自动化帐户。
* 该模块的文件夹中缺少依赖项。
* [AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) cmdlet 用于上传该模块，但你尚未提供完整的存储路径，或者尚未使用可公开访问的 URL 来加载该模块。

#### <a name="resolution"></a>解决方法

使用这些解决方案中的任何一种解决问题。

* 请确保该模块遵循以下格式： ModuleName .zip-> ModuleName 或版本号-> （hbase-runner.psm1，psd1）。
* 打开**psd1**文件，查看该模块是否有任何依赖项。 如果有，则将这些模块上传到自动化帐户。
* 请确保任何引用的 **.dll**文件都存在于 module 文件夹中。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>方案： Update-AzureModule 更新模块时暂停

#### <a name="issue"></a>问题

使用[Update-AzureModule](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook 更新 Azure 模块时，模块更新过程会挂起。

#### <a name="cause"></a>原因

使用**Update-AzureModule**时，确定同时更新的模块的默认设置为10。 如果同时更新太多模块，则更新过程容易出错。

#### <a name="resolution"></a>解决方法

同一自动化帐户中不需要所有 AzureRM 或 Az 模块。 建议只导入所需的特定模块。

> [!NOTE]
> 避免导入整个`Az.Automation`或`AzureRM.Automation`模块，这会导入所有包含的模块。

如果更新过程挂起，请将`SimultaneousModuleImportJobCount`参数添加到**Update-AzureModules**脚本，并提供低于默认值10的值。 如果实现了此逻辑，则建议从值3或5开始。 `SimultaneousModuleImportJobCount`是用于更新 Azure 模块的**AutomationAzureModulesForAccount**系统 runbook 的参数。 如果进行此调整，则更新过程的运行时间较长，但有更好的机会。 以下示例显示了该参数及其在 runbook 中的放置位置：

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>方案：无法创建或更新运行方式帐户

#### <a name="issue"></a>问题

在尝试创建或更新运行方式帐户时，接收到类似于以下错误消息的错误：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

没有创建或更新运行方式帐户所需的权限，或者资源在资源组级别锁定。

#### <a name="resolution"></a>解决方法

若要创建或更新运行方式帐户，您必须对运行方式帐户使用的不同资源具有适当的[权限](../manage-runas-account.md#permissions)。 

如果此问题是由于锁定导致的，请验证是否可以删除该锁。 然后导航到 Azure 门户中锁定的资源，右键单击该锁，然后单击 "**删除**"。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>方案：在执行 runbook 时，收到错误 "找不到 DLL ' iplpapi ' 中名为 ' GetPerAdapterInfo ' 的入口点"

#### <a name="issue"></a>问题

执行 runbook 时，会收到以下异常：

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此错误很可能是由不正确配置的[运行方式帐户](../manage-runas-account.md)导致的。

#### <a name="resolution"></a>解决方法

请确保已正确配置运行方式帐户。 然后，验证 runbook 中是否有正确的代码，以便在 Azure 中进行身份验证。 下面的示例演示使用运行方式帐户在 runbook 中对 Azure 进行身份验证的代码段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>后续步骤

如果你没有看到你的问题或无法解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。
