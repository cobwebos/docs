---
title: 启动机在 Azure 开发测试实验室中使用自动化 runbook |Microsoft Docs
description: 了解如何使用 Azure 自动化 runbook 在 Azure 开发测试实验室中的实验室中启动虚拟机。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: d80328943ae818b3bad9c0a275b74968ee33d4b7
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887181"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>使用 Azure 自动化 runbook 的实验室中顺序启动虚拟机
[Autostart](devtest-lab-set-lab-policy.md#set-autostart)开发测试实验室的功能可用于配置 Vm 以在指定时间自动启动。 但是，此功能不支持计算机按特定顺序启动。 有几种方案，这种自动化会非常有用。  一个方案是实验室中 Jumpbox VM 需要在何处进行先启动之前的其他 Vm，, 因为 Jumpbox 用作其他 vm 的 vm 的访问点。  本文介绍如何设置 Azure 自动化帐户与 PowerShell runbook 执行脚本。 该脚本使用标记在 Vm 上在实验室中以便您可以控制启动顺序，而无需更改该脚本。

## <a name="setup"></a>设置
在此示例中，在实验室中的 Vm 需要具有该标记**StartupOrder**添加具有适当的值 (0,1,2，等等。)。 指定不需要为-1 开始的任何计算机。

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户
创建 Azure 自动化帐户中的以下说明[这篇文章](../automation/automation-create-standalone-account.md)。 选择**运行方式帐户**选项创建帐户时。 创建自动化帐户后，打开**模块**页，然后选择**更新 Azure 模块**菜单栏上。 默认模块是几个版本旧，而无需更新脚本可能无法运行。

## <a name="add-a-runbook"></a>添加 runbook
现在，若要将 runbook 添加到自动化帐户中，选择**Runbook**在左侧菜单中。 选择**添加 runbook**菜单中，并按照说明上[创建 PowerShell runbook](../automation/automation-first-runbook-textual-powershell.md)。

## <a name="powershell-script"></a>PowerShell 脚本
以下脚本将订阅名称，实验室名称作为参数。 脚本的流程是在实验室中获取所有 Vm，然后解析出标记信息创建的 VM 名称和其启动顺序的列表。 该脚本演示了按顺序对 Vm 并启动 Vm。 如果某一订单号中有多个 Vm，它们会启动以异步方式使用 PowerShell 作业。 对于没有标记，设置启动值，为最后一个 (10)，这些 Vm 它们将启动最后一个，默认情况下。  如果实验室不会想要会自动启动的 VM，将标记值设置为 11，它将被忽略。

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzureRmResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzureRmContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzureRmContext -Path ($profilePath)
    Invoke-AzureRmResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzureRmResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>创建计划
有每日、 执行此脚本[创建一个计划](../automation/shared-resources/schedules.md#creating-a-schedule)自动化帐户中。 创建计划后，[将其链接到 runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook)。 

在大规模情况下有多个订阅使用多个实验室，在文件中以不同的实验室存储参数信息并将文件传递给脚本而不是单个参数。 该脚本需要修改，但核心执行将相同。 在此示例使用 Azure 自动化来执行 PowerShell 脚本，在其他选项，如在生成/发布管道中使用的任务。

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解有关 Azure 自动化的详细信息：[Azure 自动化简介](../automation/automation-intro.md)。
