---
title: 解决 Azure 自动化共享资源的错误
description: 了解如何解决 Azure 自动化共享资源的问题
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 66165a196c8b934df948f1d88b09a5859d3e792f
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804490"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>解决共享资源的错误

本文讨论了用来解决在 Azure 自动化中使用共享资源时可能会遇到的问题的解决方案。

## <a name="modules"></a>模块

### <a name="module-stuck-importing"></a>场景：某个模块在导入时停滞

#### <a name="issue"></a>问题

在 Azure 自动化中导入或更新模块时，某个模块停滞在“正在导入”状态。

#### <a name="cause"></a>原因

导入 PowerShell 模块是一个复杂的多步骤过程。 在此过程中可能会有模块无法正确导入。 如果发生此问题，则导入的模块可能会停滞在某个暂时性状态。 若要详细了解此过程，请参阅[导入 PowerShell 模块]( /powershell/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解析

若要解决此问题，必须使用 [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet 删除停滞在“正在导入”状态的模块。 然后，可以重试导入模块。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>场景：尝试将其更新后导入停滞的 AzureRM 模块

#### <a name="issue"></a>问题

尝试更新 AzureRM 模块后，并显示以下消息横幅停留在你的帐户：

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

没有更新 AzureRM 模块是具有数值名以 0 开头的资源组中的自动化帐户中的已知的问题。

#### <a name="resolution"></a>解析

若要更新 Azure 模块在自动化帐户中，它必须是字母数字名称的资源组中。 具有从 0 开始的数字名称的资源组不能在这一次更新 AzureRM 模块。

### <a name="module-fails-to-import"></a>场景：模块无法导入，或者 cmdlet 在导入后无法执行

#### <a name="issue"></a>问题

模块无法导入，或者虽然导入成功，但无法提取 cmdlet。

#### <a name="cause"></a>原因

模块无法成功导入到 Azure 自动化中的一些常见原因是：

* 结构与自动化所需的模块结构不符。
* 该模块依赖于其他模块，而后者尚未部署到自动化帐户。
* 该模块的文件夹中缺少依赖项。
* 使用了 `New-AzureRmAutomationModule` cmdlet 来上传该模块，但你尚未提供完整的存储路径，或者尚未使用可公开访问的 URL 来加载该模块。

#### <a name="resolution"></a>解析

下述解决方案中的任何一种都可以解决此问题：

* 请确保该模块遵循以下格式：ModuleName.Zip -> 模块名称或版本号 -> (ModuleName.psm1, ModuleName.psd1)
* 打开 .psd1 文件，看模块是否有任何依赖项。 如果有，则将这些模块上传到自动化帐户。
* 确保任何引用的 .dll 都存在于模块文件夹中。

### <a name="all-modules-suspended"></a>场景：Update-AzureModule.ps1 在更新模块时暂停

#### <a name="issue"></a>问题

使用 [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook 更新 Azure 模块时，模块更新过程暂停。

#### <a name="cause"></a>原因

使用 `Update-AzureModule.ps1` 脚本时，用于确定同时更新多少个模块的默认设置为 10。 如果同时更新太多模块，则更新过程容易出错。

#### <a name="resolution"></a>解析

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

### <a name="unable-create-update"></a>场景：无法创建或更新运行方式帐户

#### <a name="issue"></a>问题

在尝试创建或更新运行方式帐户时，接收到类似于以下错误消息的错误：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

没有创建或更新运行方式帐户所需的权限，或者资源在资源组级别锁定。

#### <a name="resolution"></a>解析

若要创建或更新运行方式帐户，必须对运行方式帐户使用的各种资源具有适当的权限。 要了解创建或更新运行方式帐户所需的权限，请参阅[运行方式帐户权限](../manage-runas-account.md#permissions)。

如果该问题是由某个锁引起的，请确保可以删除该锁。 然后导航到锁定的资源，右键单击该锁并选择“删除”以删除该锁。

### <a name="iphelper"></a>场景：当收到错误"找不到入口点将在 DLL 中的 GetPerAdapterInfo 名为 iplpapi.dll"执行 runbook。

#### <a name="issue"></a>问题

执行 runbook 时您会收到以下异常：

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此错误最可能是由于配置不正确[运行方式帐户](../manage-runas-account.md)。

#### <a name="resolution"></a>解析

请确保你[运行方式帐户](../manage-runas-account.md)已正确配置。 后配置正确，请确保使用 Azure 进行身份验证在 runbook 中具有适当的代码。 下面的示例演示用于在 runbook 中使用运行方式帐户进行 Azure 身份验证代码的片段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。
