---
title: 在 Azure 中的更新管理部署中管理预处理脚本和后期脚本
description: 本文介绍如何配置和管理更新部署的前脚本和后脚本。
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: f55ebb3270fdd97a1fdbbf5a56f9703c08933f9f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855335"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>管理预处理脚本和后脚本

前脚本和后脚本是在 Azure 自动化帐户中（任务前）和之后（在任务后）更新部署之前运行的 runbook。 预处理脚本和后脚本在 Azure 上下文中运行，而不是在本地运行。 预脚本在更新部署开始时运行。 后期脚本在部署结束时和任何已配置的重启之后运行。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="pre-script-and-post-script-requirements"></a>前脚本和后脚本要求

对于要用作前脚本或后脚本的 runbook，你必须将它导入到自动化帐户并[发布 runbook](manage-runbooks.md#publish-a-runbook)。

## <a name="pre-script-and-post-script-parameters"></a>前脚本和后脚本参数

在配置前脚本和后脚本时，可以像计划 runbook 一样传入参数。 参数是在创建更新部署时定义的。 操作前脚本和后脚本支持以下类型：

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

前脚本和后脚本 runbook 参数不支持布尔值、对象或数组类型。 这些值会导致 runbook 失败。 

如果需要其他对象类型，可以在 runbook 中使用自己的逻辑将它强制转换为其他类型。

除了标准 runbook 参数外，还提供了`SoftwareUpdateConfigurationRunContext`参数（类型 JSON 字符串）。 如果您在前脚本或后脚本 runbook 中定义参数，则更新部署会将其自动传入。 参数包含有关更新部署的信息，这是[SOFTWAREUPDATECONFIGURATIONS API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)返回的信息的子集。 以下各节定义关联的属性。

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 属性

|属性  |说明  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 软件更新配置的名称。        |
|SoftwareUpdateConfigurationRunId     | 运行的唯一 ID。        |
|SoftwareUpdateConfigurationSettings     | 与软件更新配置相关的属性的集合。         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 针对更新部署的操作系统。         |
|SoftwareUpdateConfigurationSettings.duration     | 每 ISO8601 运行`PT[n]H[n]M[n]S`更新部署的最长持续时间;也称为维护时段。          |
|SoftwareUpdateConfigurationSettings.Windows     | 与 Windows 计算机相关的属性的集合。         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 从更新部署中排除的 Kb 的列表。        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 为更新部署选择的更新分类。        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 更新部署的重新启动设置。        |
|azureVirtualMachines     | 更新部署中 Azure Vm 的 Resourceid 列表。        |
|nonAzureComputerNames|更新部署中非 Azure 计算机的 Fqdn 列表。|

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

可以在以下位置找到所有属性的完整示例：[按名称获取软件更新配置](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)。

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext`对象可以包含计算机的重复项。 这可能会导致在同一台计算机上运行前脚本和后脚本。 若要解决此行为，请`Sort-Object -Unique`使用仅选择唯一的 VM 名称。

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>在部署中使用脚本前脚本或后脚本

若要在更新部署中使用脚本前脚本或后脚本，请首先创建一个更新部署。 选择 "**脚本前 + 后脚本**"。 此操作会打开“选择前脚本 + 后脚本”页面。****

![选择脚本](./media/pre-post-scripts/select-scripts.png)

选择要使用的脚本。 在此示例中，我们使用**UpdateManagement-TurnOnVms** runbook。 选择 runbook 时，会打开 "**配置脚本**" 页。 选择 "**前脚本**"，然后选择 **"确定"**。

针对 **UpdateManagement-TurnOffVms** 脚本重复此过程。 但在选择**脚本类型**时，请选择 "**后脚本**"。

此时，"**选定的项**" 部分会显示您选择的脚本。 一个是脚本操作，另一个脚本是后脚本：

![选定的项](./media/pre-post-scripts/selected-items.png)

完成配置更新部署。

更新部署完成后，可以通过 "**更新部署**" 来查看结果。 正如您所看到的，为操作前脚本和后脚本提供状态：

![更新结果](./media/pre-post-scripts/update-results.png)

选择 "更新部署" "运行" 后，你会看到有关前脚本和后脚本的其他详细信息。 此时会提供运行时的脚本源的链接。

![部署运行结果](./media/pre-post-scripts/deployment-run.png)

在脚本中。

## <a name="stopping-a-deployment"></a>停止部署

如果要基于预脚本停止部署，则必须[引发](automation-runbook-execution.md#throw)异常。 否则，部署和后脚本仍将运行。 下面的代码段演示如何引发异常。

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



## <a name="interacting-with-machines"></a>与计算机交互

在自动化帐户中，操作前脚本和后期任务作为 runbook 运行，不直接在部署中的计算机上运行。 预任务和任务后任务也在 Azure 上下文中运行，不能访问非 Azure 计算机。 以下部分介绍如何直接与计算机交互，无论它们是 Azure Vm 还是非 Azure 计算机。

### <a name="interact-with-azure-machines"></a>与 Azure 计算机交互

预任务和任务后任务作为 runbook 运行，不在部署中的 Azure Vm 上本地运行。 要与 Azure Vm 交互，你必须具有以下各项：

* 一个运行方式帐户
* 要运行的 runbook

要与 Azure 计算机交互，你应使用[AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) Cmdlet 与 azure vm 进行交互。 有关如何执行此操作的示例，请参阅 runbook 示例[更新管理–使用 run 命令运行脚本](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)。

### <a name="interact-with-non-azure-machines"></a>与非 Azure 计算机交互

预任务和任务后任务在 Azure 上下文中运行，不能访问非 Azure 计算机。 若要与非 Azure 计算机交互，必须具有以下各项：

* 一个运行方式帐户
* 在计算机上安装的混合 Runbook 辅助角色
* 要在本地运行的 Runbook
* 父 runbook

若要与非 Azure 计算机交互，父 runbook 会在 Azure 上下文中运行。 此 runbook 使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) cmdlet 调用子 runbook。 必须指定 `RunOn` 参数，并提供运行脚本的混合 Runbook 辅助角色的名称。 请参阅 runbook 示例[更新管理–在本地运行脚本](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)。

## <a name="aborting-patch-deployment"></a>正在中止修补程序部署

如果你的前脚本返回错误，你可能想要中止你的部署。 若要执行此操作，必须在脚本中为导致失败的任何逻辑[引发](/powershell/module/microsoft.powershell.core/about/about_throw)错误。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>示例

可以在[脚本中心库](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)和[PowerShell 库](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)中找到前脚本和后脚本的示例，也可以通过 Azure 门户将其导入。 为此，请在自动化帐户中的 "**流程自动化**" 下选择 " **runbook 库**"。 使用“更新管理”作为筛选器。****

![库列表](./media/pre-post-scripts/runbook-gallery.png)

或者，你可以按其脚本名称搜索它们，如以下列表所示：

* 更新管理 - 启用 VM
* 更新管理 - 禁用 VM
* 更新管理 - 本地运行脚本
* 更新管理 - 前脚本/后脚本的模板
* 更新管理 - 具有 Run 命令运行脚本

> [!IMPORTANT]
> 导入 runbook 后，你必须发布这些 runbook，然后才能使用它们。 为此，请在自动化帐户中找到 runbook，选择 "**编辑**"，然后选择 "**发布**"。

示例均基于在下面的示例中定义的基本模板。 此模板可用于创建自己的 runbook，以便与前脚本和后脚本一起使用。 其中包含了在 Azure 中进行身份验证和`SoftwareUpdateConfigurationRunContext`处理参数所需的逻辑。

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
> 对于非图形 PowerShell runbook， `Add-AzAccount`和`Add-AzureRMAccount`是[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的别名。 可以使用这些 cmdlet，也可以在自动化帐户中[将模块更新](automation-update-azure-modules.md)为最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新你的模块。

## <a name="next-steps"></a>后续步骤

请继续学习以下教程，了解如何管理 Windows 虚拟机的更新：

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修补程序](automation-tutorial-update-management.md)