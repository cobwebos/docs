---
title: 在 Azure 上的更新管理部署中配置前脚本和后脚本（预览版）
description: 本文介绍如何配置和管理更新部署的前脚本和后脚本
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edbb953111fb4589539369bd9b2519b48b9b70eb
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121305"
---
# <a name="manage-pre-and-post-scripts-preview"></a>管理前脚本和后脚本（预览版）

在执行更新部署之前（前任务）和之后（后任务），可以使用前脚本和后脚本在自动化帐户中运行 PowerShell Runbook。 前脚本和后脚本在 Azure 上下文中运行，而不是在本地运行。 前脚本在更新部署开始时运行。 后脚本在部署结束时以及在配置的任何重新启动之后运行。

## <a name="runbook-requirements"></a>Runbook 要求

要将某个 Runbook 用作前脚本或后脚本，需将该 Runbook 导入到自动化帐户中并发布它。 有关此过程的详细信息，请参阅[发布 Runbook](automation-creating-importing-runbook.md#publishing-a-runbook)。

## <a name="using-a-prepost-script"></a>使用前脚本/后脚本

若要在更新部署中使用前脚本或后脚本，请先创建一个更新部署。 选择“前脚本 + 后脚本(预览版)”。 此时会打开“选择前脚本 + 后脚本”页。  

![选择脚本](./media/pre-post-scripts/select-scripts.png)

选择要使用的脚本，本示例使用了 **UpdateManagement-TurnOnVms** Runbook。 选择 Runbook 时，“配置脚本”页会打开。请提供参数值，然后选择“前脚本”。 完成后单击“确定”。

![配置脚本](./media/pre-post-scripts/configure-script.png)

针对 **UpdateManagement-TurnOffVms** 脚本重复此过程。 但是，在选择“脚本类型”时，请选择“后脚本”。

现在，“选定的项”部分会显示选择的两个脚本：一个前脚本，一个后脚本。

![选定的项](./media/pre-post-scripts/selected-items.png)

完成更新部署的配置。

更新部署完成后，可以转到“更新部署”查看结果。 可以看到，此处提供了前脚本和后脚本的状态。

![更新结果](./media/pre-post-scripts/update-results.png)

单击进入更新部署的运行后，可以看到前脚本和后脚本的其他详细信息。 此时会提供运行时的脚本源的链接。

![部署运行结果](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>传递参数

配置前脚本和后脚本时，可以像计划 Runbook 时一样传入参数。 参数是在创建更新部署时定义的。 除了标准的 Runbook 参数以外，还会提供一个附加参数。 此参数是 **SoftwareUpdateConfigurationRunContext**。 此参数是一个 JSON 字符串；如果在前脚本或后脚本中定义该参数，更新部署会自动传入该参数。 该参数包含有关更新部署的信息（[SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) 返回的信息的子集）。下表显示了变量中提供的属性：

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 属性

|属性  |Description  |
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

## <a name="samples"></a>示例

可以在[脚本中心库](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)中找到前脚本和后脚本的示例，或者可以通过 Azure 门户导入这些示例。 若要通过门户导入这些示例，请在自动化帐户中的“过程自动化”下，选择“Runbook 库”。 使用“更新管理”作为筛选器。

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
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines 
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

## <a name="interacting-with-non-azure-machines"></a>与非 Azure 计算机交互

前任务和后任务在 Azure 上下文中运行，无法访问非 Azure 计算机。 若要与非 Azure 计算机交互，必须提供：

* 一个运行方式帐户
* 在计算机上安装的混合 Runbook 辅助角色
* 要在本地运行的 Runbook
* 父 Runbook

若要与非 Azure 计算机交互，需在 Azure 上下文中运行一个父 Runbook。 此 Runbook 使用 [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet 调用子 Runbook。 必须指定 `-RunOn` 参数，并提供运行脚本的混合 Runbook 辅助角色的名称。

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="known-issues"></a>已知问题

* 使用前脚本和后脚本时，无法将对象或数组传递给参数。 Runbook 将会失败。

## <a name="next-steps"></a>后续步骤

继续学习教程来了解如何管理 Windows 虚拟机的更新。

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修补程序](automation-tutorial-update-management.md)
