---
title: 在 Azure 中的更新管理部署上配置预脚本和后脚本
description: 本文介绍如何配置和管理更新部署的预脚本和后脚本。
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 35fba966fcdb6d1c5cd7c531bb22c9c78ae16ff3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417803"
---
# <a name="manage-pre-and-post-scripts"></a>管理预脚本和后脚本

通过预脚本和后脚本，您可以在更新部署之前（任务前）和更新部署之后（任务后）在 Azure 自动化帐户中运行 PowerShell Runbook。 在 Azure 上下文中运行前脚本和后脚本，而不是本地运行。 预脚本在更新部署开始时运行。 后脚本在部署结束时和配置的任何重新启动后运行。

## <a name="runbook-requirements"></a>Runbook 要求

要将 Runbook 用作预脚本或后脚本，必须将 Runbook 导入您的自动化帐户并发布。 要了解有关此过程的更多内容，请参阅[发布 Runbook](manage-runbooks.md#publish-a-runbook)。

## <a name="using-a-pre-script-or-post-script"></a>使用预脚本或后脚本

要在更新部署中使用预脚本或后脚本，请从创建更新部署开始。 选择**预脚本 + 后脚本**。 此操作会打开“选择前脚本 + 后脚本”页面。****

![选择脚本](./media/pre-post-scripts/select-scripts.png)

选择要使用的脚本。 在此示例中，我们使用**更新管理-TurnOnVms**运行簿。 选择 Runbook 时，将打开 **"配置脚本"** 页。 选择 **"脚本前**"，然后选择 **"确定**"。

针对 **UpdateManagement-TurnOffVms** 脚本重复此过程。 但是，当您选择**脚本类型**时，请选择 **"后脚本**"。

"**选定项目**"部分现在显示所选脚本。 一个是预脚本，另一个是后脚本：

![选定的项](./media/pre-post-scripts/selected-items.png)

完成配置更新部署。

更新部署完成后，可以转到 **"更新部署"** 以查看结果。 如您所见，为预脚本和后脚本提供了状态：

![更新结果](./media/pre-post-scripts/update-results.png)

通过选择更新部署运行，将显示前脚本和脚本后的其他详细信息。 此时会提供运行时的脚本源的链接。

![部署运行结果](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>快速参考

配置预脚本和后脚本时，可以传递参数，就像安排 Runbook 一样。 参数是在创建更新部署时定义的。 脚本前和脚本后支持以下类型：

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

如果需要其他对象类型，可以在 runbook 中使用自己的逻辑将它强制转换为其他类型。

除了标准 Runbook 参数外，还提供另一个参数：**软件更新配置RunContext**

此参数是 JSON 字符串，如果您在脚本前或脚本后定义参数，则更新部署会自动传入该参数。 该参数包含有关更新部署的信息，更新部署是[软件更新配置 API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)返回的信息的子集。 

下表显示了变量中提供的属性。

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 属性

|properties  |描述  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 软件更新配置的名称。        |
|SoftwareUpdateConfigurationRunId     | 运行的唯一 ID。        |
|SoftwareUpdateConfigurationSettings     | 与软件更新配置相关的属性的集合。         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 更新部署的目标操作系统。         |
|SoftwareUpdateConfigurationSettings.duration     | 更新部署的最大持续时间按`PT[n]H[n]M[n]S`ISO8601 运行;也称为*维护窗口*。          |
|SoftwareUpdateConfigurationSettings.Windows     | 与 Windows 计算机相关的属性的集合。         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 从更新部署中排除的 QB 的列表。        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 更新为更新部署选择的分类。        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 重新启动更新部署的设置。        |
|azureVirtualMachines     | 更新部署中 Azure VM 的资源 ID 列表。        |
|nonAzureComputerNames|更新部署中非 Azure 计算机 FQDN 的列表。|

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

具有所有属性的完整示例，请于：[按名称获取软件更新配置](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)。

> [!NOTE]
> 该`SoftwareUpdateConfigurationRunContext`对象可以包含计算机的重复条目。 这可能会导致脚本前和脚本后在同一台计算机上多次运行。 要解决此问题，请使用 仅在`Sort-Object -Unique`脚本中选择唯一的 VM 名称。


## <a name="stopping-a-deployment"></a>停止部署

如果要停止基于预脚本的部署，则必须[引发](automation-runbook-execution.md#throw)异常。 如果没有，部署和后脚本仍将运行。 以下代码段演示如何引发异常。

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

## <a name="samples"></a>示例

在[脚本中心库](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)和[PowerShell 库中](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)可以找到预脚本和后脚本的示例，也可以通过 Azure 门户导入它们。 为此，在您的自动化帐户中，在 **"过程自动化**"下，选择**Runbook 库**。 使用“更新管理”作为筛选器。****

![库列表](./media/pre-post-scripts/runbook-gallery.png)

或者，您可以按脚本名称搜索它们，如以下列表所示：

* 更新管理 - 启用 VM
* 更新管理 - 禁用 VM
* 更新管理 - 本地运行脚本
* 更新管理 - 前脚本/后脚本的模板
* 更新管理 - 具有 Run 命令运行脚本

> [!IMPORTANT]
> 导入 Runbook 后，必须先发布它们，然后才能使用它们。 为此，请在自动化帐户中找到 Runbook，选择 **"编辑**"，然后选择 **"发布**"。

这些示例都基于以下示例中定义的基本模板。 此模板可用于创建自己的 Runbook，以便与预脚本和后脚本一起使用。 包括使用 Azure 进行身份验证和处理`SoftwareUpdateConfigurationRunContext`参数的必要逻辑。

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

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>与机器交互

任务前和任务后作为自动化帐户中的 Runbook 运行，而不是直接在部署中的计算机上运行。 任务前和任务后也会在 Azure 上下文中运行，并且无法访问非 Azure 计算机。 以下各节演示如何与计算机直接交互，无论是 Azure VM 还是非 Azure 计算机。

### <a name="interacting-with-azure-machines"></a>与 Azure 计算机交互

预任务和任务后任务作为 Runbook 运行，并且不会本机在部署中的 Azure VM 上运行。 要与 Azure VM 进行交互，必须具有以下项目：

* 一个运行方式帐户
* 要运行的 Runbook

要与 Azure 计算机进行交互，应使用[调用 AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) cmdlet 与 Azure VM 进行交互。 有关如何执行此操作的示例，请参阅 Runbook 示例["更新管理 — 使用 Run 命令运行脚本](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)"。

### <a name="interacting-with-non-azure-machines"></a>与非 Azure 计算机交互

任务前和任务后在 Azure 上下文中运行，并且无法访问非 Azure 计算机。 要与非 Azure 计算机进行交互，必须具有以下项：

* 一个运行方式帐户
* 在计算机上安装的混合 Runbook 辅助角色
* 要在本地运行的 Runbook
* 父运行簿

要与非 Azure 计算机进行交互，在 Azure 上下文中运行父 Runbook。 此 Runbook 使用 [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet 调用子 Runbook。 必须指定 `-RunOn` 参数，并提供运行脚本的混合 Runbook 辅助角色的名称。 有关详细信息，请参阅 runbook 示例["更新管理 " 在本地运行脚本](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)。

## <a name="abort-patch-deployment"></a>中止修补程序部署

如果预脚本返回错误，则可能需要中止部署。 为此，您必须在脚本中[为](/powershell/module/microsoft.powershell.core/about/about_throw)任何构成失败的逻辑引发错误。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>已知问题

* 使用预脚本和后脚本时，不能将布尔、对象或数组传递给参数。 如果这样做，则 Runbook 将失败。 有关受支持类型的完整列表，请参阅[传递参数](#passing-parameters)。

## <a name="next-steps"></a>后续步骤

继续学习以下教程，了解如何管理 Windows 虚拟机的更新：

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修补程序](automation-tutorial-update-management.md)

