---
title: 配置更新管理部署在 Azure 中的 pre 和 post 脚本
description: 本文介绍如何配置和管理更新部署的前脚本和后脚本
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8a11602919a8b68a078b0b2690411358b4b5f814
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063495"
---
# <a name="manage-pre-and-post-scripts"></a>管理 pre 和 post 脚本

在执行更新部署之前（前任务）和之后（后任务），可以使用前脚本和后脚本在自动化帐户中运行 PowerShell Runbook。 前脚本和后脚本在 Azure 上下文中运行，而不是在本地运行。 操作前脚本运行更新部署的开始处。 后脚本在部署结束时以及在配置的任何重新启动之后运行。

## <a name="runbook-requirements"></a>Runbook 要求

要将某个 Runbook 用作前脚本或后脚本，需将该 Runbook 导入到自动化帐户中并发布它。 有关此过程的详细信息，请参阅[发布 Runbook](manage-runbooks.md#publish-a-runbook)。

## <a name="using-a-prepost-script"></a>使用前脚本/后脚本

若要在更新部署中使用前脚本或后脚本，请先创建一个更新部署。 选择**操作前脚本 + 后处理脚本**。 此操作会打开“选择前脚本 + 后脚本”页面。   

![选择脚本](./media/pre-post-scripts/select-scripts.png)

选择要使用的脚本，本示例使用了 **UpdateManagement-TurnOnVms** Runbook。 当选择 runbook**配置脚本**页随即打开，选择**前脚本**。 完成后单击“确定”  。

针对 **UpdateManagement-TurnOffVms** 脚本重复此过程。 但是，在选择“脚本类型”时，请选择“后脚本”。  

现在，“选定的项”部分会显示选择的两个脚本：一个前脚本，一个后脚本。 

![选定的项](./media/pre-post-scripts/selected-items.png)

完成更新部署的配置。

更新部署完成后，可以转到“更新部署”查看结果。  可以看到，此处提供了前脚本和后脚本的状态。

![更新结果](./media/pre-post-scripts/update-results.png)

单击进入更新部署的运行后，可以看到前脚本和后脚本的其他详细信息。 此时会提供运行时的脚本源的链接。

![部署运行结果](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>传递参数

配置前脚本和后脚本时，可以像计划 Runbook 时一样传入参数。 参数是在创建更新部署时定义的。 前脚本和后脚本支持以下类型：

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

除了标准的 Runbook 参数以外，还提供了一个附加参数。 此参数是 **SoftwareUpdateConfigurationRunContext**。 此参数是一个 JSON 字符串，并且如果前置或后置脚本中定义该参数，则会自动传递中的更新部署。 该参数包含有关更新部署的信息（[SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) 返回的信息的子集）。下表显示了变量中提供的属性：

## <a name="stopping-a-deployment"></a>停止某一部署

如果你想要停止基于前脚本的部署，则必须[引发](automation-runbook-execution.md#throw)异常。 如果您不会引发异常，仍将运行的部署和后脚本。 [示例 runbook](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0)在库中显示了如何执行此操作。 下面是该 runbook 的代码段。

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

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 属性

|属性  |描述  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 软件更新配置的名称        |
|SoftwareUpdateConfigurationRunId     | 运行的唯一 ID。        |
|SoftwareUpdateConfigurationSettings     | 与软件更新配置相关的属性集合         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 面向更新部署的操作系统         |
|SoftwareUpdateConfigurationSettings.duration     | 符合 ISO8601 的更新部署最长运行持续时间，格式为 `PT[n]H[n]M[n]S`，也称为“维护时段”          |
|SoftwareUpdateConfigurationSettings.Windows     | 与 Windows 计算机相关的属性集合         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 从更新部署中排除的 KB 数列表        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 为更新部署选择的更新分类        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 更新部署的重新启动设置        |
|azureVirtualMachines     | 更新部署中 Azure VM 的 resourceIds 列表        |
|nonAzureComputerNames|更新部署中的非 Azure 计算机 FQDN 列表|

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

可以在以下位置找到包含所有属性的完整示例：[软件更新配置 - 按名称获取](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext`对象可包含计算机的重复条目。 这会导致在同一台计算机上运行多次的 Pre 和 Post 脚本。 解决此行为，使用`Sort-Object -Unique`来选择您的脚本中的唯一 VM 名称。

## <a name="samples"></a>示例

可以在[脚本中心库](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)中找到前脚本和后脚本的示例，或者可以通过 Azure 门户导入这些示例。 若要通过门户导入这些示例，请在自动化帐户中的“过程自动化”下，选择“Runbook 库”。   使用“更新管理”作为筛选器。 

![库列表](./media/pre-post-scripts/runbook-gallery.png)

或者，可按脚本名称搜索这些示例，如以下列表中所示：

* 更新管理 - 启用 VM
* 更新管理 - 禁用 VM
* 更新管理 - 本地运行脚本
* 更新管理 - 前脚本/后脚本的模板
* 更新管理 - 具有 Run 命令运行脚本

> [!IMPORTANT]
> 导入 Runbook 后，必须先**发布**，然后才可以使用它们。 为此，请在自动化帐户中找到该 Runbook，选择“编辑”，然后单击“发布”。  

所有这些示例基于以下示例中定义的基本模板。 使用此模板可以创建自己的 Runbook 来配合前脚本和后脚本。 此模板还包含了用于在 Azure 中进行身份验证以及处理 `SoftwareUpdateConfigurationRunContext` 参数的逻辑。

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
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

## <a name="interacting-with-machines"></a>与计算机交互

Pre 和 post 的任务作为 runbook 在自动化帐户中并不是直接在你的部署在计算机上运行。 Pre 和 post 任务还 Azure 上下文中运行，并且不能访问非 Azure 计算机。 以下部分介绍了如何交互，与计算机直接它们是 Azure VM 或非 Azure 计算机：

### <a name="interacting-with-azure-machines"></a>与 Azure 机交互

Pre 和 post 任务以 runbook 的形式运行，并不在你的部署中的 Azure Vm 上本机运行。 若要与 Azure 虚拟机进行交互，必须具有以下各项：

* 一个运行方式帐户
* 你想要运行 runbook

若要与 Azure 机交互时，应使用[Invoke-azurermvmruncommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) cmdlet 与 Azure 虚拟机进行交互。 有关如何执行此操作的示例，请参阅 runbook 示例[更新管理-具有运行命令运行脚本](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)。

### <a name="interacting-with-non-azure-machines"></a>与非 Azure 计算机交互

前任务和后任务在 Azure 上下文中运行，无法访问非 Azure 计算机。 若要与非 Azure 计算机进行交互，必须具有以下项：

* 一个运行方式帐户
* 在计算机上安装的混合 Runbook 辅助角色
* 要在本地运行的 Runbook
* 父 Runbook

若要与非 Azure 计算机交互，需在 Azure 上下文中运行一个父 Runbook。 此 Runbook 使用 [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet 调用子 Runbook。 必须指定 `-RunOn` 参数，并提供运行脚本的混合 Runbook 辅助角色的名称。 有关如何执行此操作的示例，请参阅 runbook 示例[更新管理的本地运行脚本](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)。

## <a name="abort-patch-deployment"></a>中止修补程序部署

如果前脚本返回错误，您可能想要中止部署。 若要执行此操作，必须[引发](/powershell/module/microsoft.powershell.core/about/about_throw)中您的脚本的任何逻辑的行为就构成了失败的错误。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>已知问题

* 你无法向参数传递一个布尔值、 对象或数组时使用 pre 和 post 脚本。 Runbook 将会失败。 有关支持的类型的完整列表，请参阅[参数](#passing-parameters)。

## <a name="next-steps"></a>后续步骤

继续学习教程来了解如何管理 Windows 虚拟机的更新。

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修补程序](automation-tutorial-update-management.md)

