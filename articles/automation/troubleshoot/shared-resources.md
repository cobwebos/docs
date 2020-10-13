---
title: 排查 Azure 自动化共享资源问题
description: 本文介绍了如何排查和解决 Azure 自动化共享资源的问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c6bdc09d37cf29458346eaea360b4cd9e0d1226f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187160"
---
# <a name="troubleshoot-shared-resource-issues"></a>排查共享资源问题

本文讨论了在使用 Azure 自动化中的[共享资源](../automation-intro.md#shared-resources)时可能会出现的问题。

## <a name="modules"></a>模块

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>场景：在导入期间模块处于停滞状态

#### <a name="issue"></a>问题

导入或更新 Azure 自动化模块时，模块停滞在“正在导入”状态。

#### <a name="cause"></a>原因

由于导入 PowerShell 模块是一个复杂的多步骤过程，模块可能不会正确导入，并且停滞在某种暂时性状态。 若要详细了解导入过程，请参阅[导入 PowerShell 模块](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解决方法

若要解决此问题，必须使用 [Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) cmdlet 删除处于停滞状态的模块。 然后，可以重试导入模块。

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>场景：尝试更新后，AzureRM 模块在导入期间停滞

#### <a name="issue"></a>问题

尝试更新 AzureRM 模块后，帐户中一直显示包含以下消息的横幅：

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

更新自动化帐户中的 AzureRM 模块时存在一个已知问题。 具体而言，如果模块所在的资源组具有以 0 开头的数字名称，则会出现此问题。

#### <a name="resolution"></a>解决方法

若要更新自动化帐户中的 AzureRM 模块，该帐户必须位于采用字母数字名称的资源组中。 采用以 0 开头的数字名称的资源组目前无法更新 AzureRM 模块。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>场景：模块无法导入，或者 cmdlet 在导入后无法执行

#### <a name="issue"></a>问题

模块无法导入，或者虽然导入成功，但无法提取 cmdlet。

#### <a name="cause"></a>原因

模块无法成功导入到 Azure 自动化中的一些常见原因是：

* 结构与自动化所需的结构不匹配。
* 该模块依赖于其他模块，而后者尚未部署到自动化帐户。
* 该模块的文件夹中缺少依赖项。
* 使用了 [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) cmdlet 来上传该模块，但你未提供完整的存储路径，或者未使用可公开访问的 URL 来加载该模块。

#### <a name="resolution"></a>解决方法

使用以下任一解决方法来解决此问题：

* 确保该模块遵循以下格式：ModuleName.zip -> 模块名称或版本号 -> (ModuleName.psm1, ModuleName.psd1)。
* 打开 **.psd1** 文件，查看该模块是否有任何依赖项。 如果有，则将这些模块上传到自动化帐户。
* 确保所引用的任何 **.dll** 文件都存在于模块文件夹中。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>场景：Update-AzureModule.ps1 在更新模块时暂停

#### <a name="issue"></a>问题

使用 [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook 更新 Azure 模块时，模块更新过程暂停。

#### <a name="cause"></a>原因

对于此 Runbook，用于确定可同时更新的模块数的默认设置为 10。 如果同时更新太多模块，则更新过程容易出错。

#### <a name="resolution"></a>解决方法

在同一个自动化帐户中需要使用所有 AzureRM 或 Az 模块的情况并不常见。 你应当只导入所需的特定模块。

> [!NOTE]
> 避免导入整个 `Az.Automation` 或 `AzureRM.Automation` 模块，否则会导入包含的所有模块。

如果更新过程暂停，请向 **Update-AzureModules.ps1** 脚本添加 `SimultaneousModuleImportJobCount` 参数，并提供小于默认值 10 的值。 如果实现此逻辑，请尝试从值 3 或 5 开始。 `SimultaneousModuleImportJobCount` 是用于更新 Azure 模块的 **Update-AutomationAzureModulesForAccount** 系统 Runbook 的一个参数。 如果进行此调整，更新过程的运行时间会变长，但会提高成功完成的可能性。 以下示例显示了该参数及其在 runbook 中的放置位置：

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>场景：无法创建或更新运行方式帐户

#### <a name="issue"></a>问题

尝试创建或更新运行方式帐户时，收到类似于以下内容的错误：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

你没有创建或更新运行方式帐户所需的权限，或者资源在资源组级别被锁定。

#### <a name="resolution"></a>解决方法

若要创建或更新运行方式帐户，你必须对运行方式帐户使用的各种资源具有适当的[权限](../manage-runas-account.md#permissions)。 

如果问题是由某个锁造成的，请验证是否可以删除该锁。 在 Azure 门户中转到被锁定的资源，右键单击该锁，然后选择“删除”。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>场景：执行 Runbook 时收到错误“在 DLL 'iplpapi.dll' 中找不到名为 'GetPerAdapterInfo' 的入口点”

#### <a name="issue"></a>问题

执行 Runbook 时收到以下异常：

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此错误很可能是由于错误配置了[运行方式帐户](../manage-runas-account.md)而造成的。

#### <a name="resolution"></a>解决方法

确保正确配置运行方式帐户。 然后，验证在 Runbook 中是否提供了用于在 Azure 中进行身份验证的正确代码。 以下示例展示了 Runbook 中使用运行方式帐户向 Azure 进行身份验证的代码片段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>后续步骤

如果本文未解决你的问题，请尝试通过以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 联系 [@AzureSupport](https://twitter.com/azuresupport)。 这是官方的 Microsoft Azure 帐户，它可以连接到 Azure 社区中的合适资源，为你提供解答、支持和专家建议。
* 提出 Azure 支持事件。 转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。

