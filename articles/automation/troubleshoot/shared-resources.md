---
title: 解决 Azure 自动化共享资源的错误
description: 了解如何使用支持 Runbook 的 Azure 自动化共享资源来排除和解决问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278319"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>解决共享资源的错误

本文讨论了用来解决在 Azure 自动化中使用共享资源时可能会遇到的问题的解决方案。

## <a name="modules"></a>模块

### <a name="scenario-a-module-is-stuck-importing"></a><a name="module-stuck-importing"></a>场景：某个模块在导入时停滞

#### <a name="issue"></a>问题

在 Azure 自动化中导入或更新模块时，某个模块停滞在“正在导入”**** 状态。

#### <a name="cause"></a>原因

导入 PowerShell 模块是一个复杂的多步骤过程。 在此过程中可能会有模块无法正确导入。 如果发生此问题，则导入的模块可能会停滞在某个暂时性状态。 若要详细了解此过程，请参阅[导入 PowerShell 模块](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解决方法

若要解决此问题，必须使用 [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet 删除停滞在“正在导入”**** 状态的模块。 然后，可以重试导入模块。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-importing-after-trying-to-update-them"></a><a name="update-azure-modules-importing"></a>方案：AzureRM 模块在尝试更新后已停止导入

#### <a name="issue"></a>问题

尝试更新 AzureRM 模块后，帐户中将保留带有以下消息的横幅：

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

更新自动化帐户中的 AzureRM 模块存在已知问题，该模块位于具有以 0 开头的数字名称的资源组中。

#### <a name="resolution"></a>解决方法

要更新自动化帐户中的 Azure 模块，它必须位于具有字母数字名称的资源组中。 以 0 开头的具有数字名称的资源组此时无法更新 AzureRM 模块。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>场景：模块无法导入，或者 cmdlet 在导入后无法执行

#### <a name="issue"></a>问题

模块无法导入，或者虽然导入成功，但无法提取 cmdlet。

#### <a name="cause"></a>原因

模块无法成功导入到 Azure 自动化中的一些常见原因是：

* 结构与自动化所需的模块结构不符。
* 该模块依赖于其他模块，而后者尚未部署到自动化帐户。
* 该模块的文件夹中缺少依赖项。
* 将使用 `New-AzureRmAutomationModule` cmdlet 来上传该模块，但尚未提供完整的存储路径，或者尚未使用可公开访问的 URL 来加载该模块。

#### <a name="resolution"></a>解决方法

下述解决方案中的任何一种都可以解决此问题：

* 确保模块遵循以下格式：模块名称.Zip**->** 模块名称或版本号**->**（模块名称.psm1，模块名称.psd1）
* 打开 .psd1 文件，看模块是否有任何依赖项。 如果有，则将这些模块上传到自动化帐户。
* 确保任何引用的 .dll 都存在于模块文件夹中。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>方案：更新-AzureModule.ps1 更新模块时挂起

#### <a name="issue"></a>问题

使用 [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook 更新 Azure 模块时，模块更新过程暂停。

#### <a name="cause"></a>原因

使用 `Update-AzureModule.ps1` 脚本时，用于确定同时更新多少个模块的默认设置为 10。 如果同时更新太多模块，则更新过程容易出错。

#### <a name="resolution"></a>解决方法

在同一个自动化帐户中需要使用所有 AzureRM 模块的情况并不常见。 建议仅导入所需的 AzureRM 模块。

> [!NOTE]
> 避免导入 **AzureRM** 模块。 导入 **AzureRM** 模块会导致导入所有 **AzureRM.\*** 模块，因此不建议这样做。

如果更新过程暂停，则需要向 `Update-AzureModules.ps1` 脚本添加 `SimultaneousModuleImportJobCount` 参数，并提供低于默认值 10 的值。 如果实现此逻辑，建议先从值 3 或 5 开始。 `SimultaneousModuleImportJobCount` 是用于更新 Azure 模块的 `Update-AutomationAzureModulesForAccount` 系统 runbook 的参数。 这项更改会延长该过程的运行时间，但会提高其完成几率。 以下示例显示了该参数及其在 runbook 中的放置位置：

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

若要创建或更新运行方式帐户，必须对运行方式帐户使用的各种资源具有适当的权限。 要了解创建或更新运行方式帐户所需的权限，请参阅[运行方式帐户权限](../manage-runas-account.md#permissions)。

如果该问题是由某个锁引起的，请确保可以删除该锁。 然后导航到锁定的资源，右键单击该锁并选择“删除”以删除该锁****。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>方案：在执行 Runbook 时，您会收到错误"无法在 DLL "iplpapi.dll"中找到名为"GetPerAdapterinfo"的入口点。

#### <a name="issue"></a>问题

执行 Runbook 时，您会收到以下异常：

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此错误很可能是由配置不正确的["作为帐户运行"](../manage-runas-account.md)引起的。

#### <a name="resolution"></a>解决方法

确保正确配置了["以帐户身份运行](../manage-runas-account.md)"。 正确配置后，请确保 Runbook 中具有使用 Azure 进行身份验证的正确代码。 下面的示例显示了使用"运行作为帐户"在 Runbook 中向 Azure 进行身份验证的代码段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帐户连接，通过将 Azure 社区连接到正确的资源（答案、支持和专家），从而改善客户体验。
* 如需更多帮助，可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
