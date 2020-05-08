---
title: 使用 Azure 开发测试实验室中的自动化 runbook 启动计算机
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: df6d7943a5344b4288dfe369dcce9087b894984f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580587"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>使用 Azure 自动化 runbook 按顺序启动实验室中的虚拟机
使用开发测试实验室的[自动](devtest-lab-set-lab-policy.md#set-autostart)启动功能，可以将 vm 配置为在指定时间自动启动。 但是，此功能不支持按特定顺序启动计算机。 在某些情况下，这种类型的自动化会很有用。  一种方案是，实验室中的 Jumpbox VM 首先需要在其他 Vm 之前启动，因为 Jumpbox 用作其他 Vm 的访问点。  本文介绍如何设置 Azure 自动化帐户，其中包含用于执行脚本的 PowerShell runbook。 此脚本使用实验室中的 Vm 上的标记来控制启动顺序，而无需更改脚本。

## <a name="setup"></a>安装
在此示例中，实验室中的 Vm 需要使用适当的值（0、1、2等）添加标记**StartupOrder** 。 指定任何不需要作为-1 启动的计算机。

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户
按照[本文](../automation/automation-create-standalone-account.md)中的说明创建 Azure 自动化帐户。 创建帐户时，请选择 "**运行方式帐户**" 选项。 创建自动化帐户后，打开 "**模块**" 页，并在菜单栏上选择 "**更新 Azure 模块**"。 默认模块是多个旧版本，无需更新脚本即可工作。

## <a name="add-a-runbook"></a>添加 runbook
现在，若要将 runbook 添加到自动化帐户，请在左侧菜单中选择 " **runbook** "。 在菜单上选择 "**添加 runbook** "，并按照说明[创建 PowerShell runbook](../automation/automation-first-runbook-textual-powershell.md)。

## <a name="powershell-script"></a>PowerShell 脚本
以下脚本采用订阅名称，将实验室名称作为参数。 脚本的流是获取实验室中的所有 Vm，然后分析出标记信息，以创建 VM 名称及其启动顺序列表。 该脚本会按顺序遍历 Vm，并启动 Vm。 如果有多个 Vm 的特定订单号，它们将使用 PowerShell 作业异步启动。 对于没有标记的 Vm，请将 "启动值" 设置为最后一个（10），默认情况下，它们将启动。  如果实验室不希望 VM 自动安装，请将标记值设置为11，它将被忽略。

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
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

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
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
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
        $returnStatus = Invoke-AzResourceAction `
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
若要每天执行此脚本，请在自动化帐户中[创建一个计划](../automation/shared-resources/schedules.md#create-a-schedule)。 创建计划后，请将[其链接到 runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook)。 

在有多个具有多个实验室的订阅的大规模情况下，将参数信息存储在不同实验室的文件中，并将该文件传递给脚本，而不是单独的参数。 脚本需要修改，但核心执行将是相同的。 虽然此示例使用 Azure 自动化来执行 PowerShell 脚本，但还有其他一些选项，如在生成/发布管道中使用任务。

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解有关 Azure 自动化的详细信息： [Azure 自动化简介](../automation/automation-intro.md)。
