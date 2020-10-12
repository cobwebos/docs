---
title: 在 Azure 的更新管理部署中管理操作前脚本和操作后脚本
description: 本文介绍如何配置和管理更新部署的操作前脚本和操作后脚本。
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 32a8214a5bd6844c9177c7af159a453bc3673efc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87449919"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>管理前脚本和后脚本

操作前脚本和操作后脚本是在执行更新部署之前（执行任务前）和之后（执行任务后）要在 Azure 自动化帐户中运行的 runbook。 操作前脚本和操作后脚本在 Azure 上下文中运行，而不是在本地运行。 操作前脚本在更新部署开始时运行。 操作后脚本在部署结束时以及在配置的任何重新启动之后运行。

## <a name="pre-script-and-post-script-requirements"></a>操作前脚本和操作后脚本要求

若要将某个 runbook 用作操作前脚本或操作后脚本，必须将其导入到自动化帐户中并[发布 runbook](../manage-runbooks.md#publish-a-runbook)。

## <a name="pre-script-and-post-script-parameters"></a>操作前脚本和操作后脚本参数

配置操作前脚本和操作后脚本时，可以像计划 runbook 时一样传入参数。 参数是在创建更新部署时定义的。 操作前脚本和操作后脚本支持以下类型：

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

操作前脚本和操作后脚本 runbook 参数不支持布尔值、对象或数组类型。 这些值会导致 runbook 失败。 

如果需要其他对象类型，可以在 runbook 中使用自己的逻辑将它强制转换为其他类型。

除标准 runbook 参数外，还提供 `SoftwareUpdateConfigurationRunContext` 参数（类型为 JSON 字符串）。 如果在操作前脚本或操作后脚本 runbook 中定义该参数，更新部署会自动传入该参数。 该参数包含有关更新部署的信息，这是 [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) 返回的信息的子集。 以下部分定义关联的属性。

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 属性

|属性  |说明  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 软件更新配置的名称。        |
|SoftwareUpdateConfigurationRunId     | 运行的唯一 ID。        |
|SoftwareUpdateConfigurationSettings     | 与软件更新配置相关的属性的集合。         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 面向更新部署的操作系统。         |
|SoftwareUpdateConfigurationSettings.duration     | 符合 ISO8601 的更新部署运行的最长持续时间，格式为 `PT[n]H[n]M[n]S`；也称为“维护时段”。          |
|SoftwareUpdateConfigurationSettings.Windows     | 与 Windows 计算机相关的属性的集合。         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 从更新部署中排除的 KB 的列表。        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 为更新部署选择的更新分类。        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 更新部署的重新启动设置。        |
|azureVirtualMachines     | 更新部署中 Azure VM 的 resourceId 的列表。        |
|nonAzureComputerNames|更新部署中的非 Azure 计算机 FQDN 的列表。|

下面是传入 **SoftwareUpdateConfigurationRunContext** 参数的 JSON 字符串示例：

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

可以在以下位置找到包含所有属性的完整示例：[按名称获取软件更新配置](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)。

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` 对象可以包含计算机的重复项。 这可能导致操作前脚本和操作后脚本在同一计算机上多次运行。 若要解决此问题，请使用 `Sort-Object -Unique` 仅选择唯一 VM 名称。

> [!NOTE]
> 目前仅支持 PowerShell runbook 作为前期/后期脚本。 目前不支持将其他 runbook 类型（如 Python、图形、PowerShell 工作流和图形 PowerShell 工作流）作为前期/后期脚本。

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>在部署中使用操作前脚本或操作后脚本

若要在更新部署中使用操作前脚本或操作后脚本，请先创建一个更新部署。 选择“操作前脚本 + 操作后脚本”。 此操作会打开“选择前脚本 + 后脚本”页面。

![选择脚本](./media/update-mgmt-pre-post-scripts/select-scripts.png)

选择要使用的脚本。 在此示例中，我们使用 UpdateManagement-TurnOnVms runbook。 选择 runbook 后，“配置脚本”页会打开。 选择“操作前脚本”，然后选择“确定”。

针对 **UpdateManagement-TurnOffVms** 脚本重复此过程。 但是，选择“脚本类型”时，请选择“操作后脚本”。

“选定项”部分现在显示选择的两个脚本。 一个是操作前脚本，另一个是操作后脚本：

![选定的项](./media/update-mgmt-pre-post-scripts/selected-items.png)

完成更新部署的配置。

更新部署完成后，可以转到“更新部署”查看结果。 可以看到，此处提供了操作前脚本和操作后脚本的状态：

![更新结果](./media/update-mgmt-pre-post-scripts/update-results.png)

通过选择更新部署运行，系统将显示操作前脚本和操作后脚本的其他详细信息。 此时会提供运行时的脚本源的链接。

![部署运行结果](./media/update-mgmt-pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>停止部署

如果要停止基于操作前脚本的部署，则必须[引发](../automation-runbook-execution.md#throw)异常。 否则，仍将运行部署和操作后脚本。 以下代码片段演示如何引发异常。

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="interact-with-machines"></a>与计算机交互

操作前脚本和执行后任务在自动化帐户中作为 runbook 运行，而不是直接在部署中的计算机上运行。 执行前任务和执行后任务也在 Azure 上下文中运行，并且无权访问非 Azure 计算机。 以下部分演示如何直接与计算机交互，而无论它们是 Azure VM 还是非 Azure 计算机。

### <a name="interact-with-azure-machines"></a>与 Azure 计算机交互

执行前任务和执行后任务作为 runbook 运行，而不是在部署中的 Azure VM 本机上运行。 若要与 Azure VM 交互，必须具有以下各项：

* 一个运行方式帐户
* 要运行的 runbook

若要与 Azure 计算机交互，应使用 [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) cmdlet 与 Azure VM 进行交互。 有关如何执行此操作的示例，请参阅 runbook 示例[更新管理 - 使用 Run 命令运行脚本](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)。

### <a name="interact-with-non-azure-machines"></a>与非 Azure 计算机交互

执行前任务和执行后任务在 Azure 上下文中运行，并且无权访问非 Azure 计算机。 若要与非 Azure 计算机交互，必须具有以下项：

* 一个运行方式帐户
* 在计算机上安装的混合 Runbook 辅助角色
* 要在本地运行的 Runbook
* 父 runbook

若要与非 Azure 计算机交互，需在 Azure 上下文中运行父 runbook。 此 runbook 使用 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) cmdlet 调用子 runbook。 必须指定 `RunOn` 参数，并提供运行脚本的混合 Runbook 辅助角色的名称。 请参阅 runbook 示例[更新管理 - 在本地运行脚本](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)。

## <a name="abort-patch-deployment"></a>中止修补程序部署

如果操作前脚本返回错误，则可能需要中止部署。 为此，必须在脚本中为可能导致失败的所有逻辑的[引发](/powershell/module/microsoft.powershell.core/about/about_throw)错误。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>示例

可以在[脚本中心库](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)和 [PowerShell 库](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)中找到操作前脚本和操作后脚本的示例，也可以通过 Azure 门户导入这些示例。 为此，请在自动化帐户的“流程自动化”下，选择“Runbook 库”。 使用“更新管理”作为筛选器。

![库列表](./media/update-mgmt-pre-post-scripts/runbook-gallery.png)

或者，可按脚本名称进行搜索，如以下列表中所示：

* 更新管理 - 启用 VM
* 更新管理 - 禁用 VM
* 更新管理 - 本地运行脚本
* 更新管理 - 前脚本/后脚本的模板
* 更新管理 - 具有 Run 命令运行脚本

> [!IMPORTANT]
> 导入 runbook 后，必须先发布，然后才可以使用它们。 为此，请在自动化帐户中找到该 runbook，选择“编辑”，然后选择“发布” 。

所有这些示例均基于以下示例中定义的基本模板。 使用此模板可以创建自己的 runbook 来配合操作前脚本和操作后脚本。 其中还包含用于在 Azure 中进行身份验证以及处理 `SoftwareUpdateConfigurationRunContext` 参数所需的逻辑。

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> 对于非图形 PowerShell runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) 的别名。 可以使用这些 cmdlet，也可以在自动化帐户中[将模块更新](../automation-update-azure-modules.md)为最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新你的模块。

## <a name="next-steps"></a>后续步骤

* 有关更新管理的详细信息，请参阅[管理 VM 的更新和修补程序](update-mgmt-manage-updates-for-vm.md)。
