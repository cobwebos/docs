---
title: 使用 Azure 开发人员测试实验室中的自动化运行簿启动计算机
description: 了解如何使用 Azure 自动化运行簿在 Azure 开发人员测试实验室的实验室中启动虚拟机。
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
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166313"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>使用 Azure 自动化运行簿在实验室中按顺序启动虚拟机
DevTest Labs 的[自动启动](devtest-lab-set-lab-policy.md#set-autostart)功能允许您配置 VM 在指定时间自动启动。 但是，此功能不支持按特定顺序启动的计算机。 在几种方案中，这种类型的自动化将很有用。  一种方案是，在实验室中需要先启动一个跳转盒 VM，然后再在其他 VM 之前启动，因为跳转盒用作对其他 VM 的接入点。  本文介绍如何使用执行脚本的 PowerShell Runbook 设置 Azure 自动化帐户。 该脚本使用实验室中 VM 上的标记来控制启动顺序，而无需更改脚本。

## <a name="setup"></a>设置
在此示例中，实验室中的 VM 需要添加带有相应值（0，1，2 等）的标记**启动订单**。 指定不需要启动的任何计算机为 -1。

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户
通过按照[本文](../automation/automation-create-standalone-account.md)中的说明创建 Azure 自动化帐户。 创建帐户时选择"**以帐户身份运行**"选项。 创建自动化帐户后，打开 **"模块"** 页，然后选择菜单栏上的 **"更新 Azure 模块**"。 默认模块是多个旧版本，如果没有更新，脚本可能无法正常工作。

## <a name="add-a-runbook"></a>添加运行簿
现在，要向自动化帐户添加 Runbook，请在左侧菜单上选择**Runbook。** 选择**在菜单上添加运行簿**，然后按照说明[创建 PowerShell Runbook](../automation/automation-first-runbook-textual-powershell.md)。

## <a name="powershell-script"></a>PowerShell 脚本
以下脚本采用订阅名称，实验室名称作为参数。 脚本的流是获取实验室中的所有 VM，然后解析标记信息以创建 VM 名称及其启动顺序的列表。 脚本按顺序遍穿 VM 并启动 VM。 如果特定订单号中有多个 VM，则使用 PowerShell 作业异步启动它们。 对于没有标记的 VM，将启动值设置为最后一个 （10），默认情况下，它们将最后启动。  如果实验室不希望自动启动 VM，则将标记值设置为 11，并将忽略该标记值。

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
要让此脚本每天执行，请在自动化帐户中[创建计划](../automation/shared-resources/schedules.md#creating-a-schedule)。 创建计划后，[将其链接到 Runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook)。 

在具有多个实验室的多个订阅的大规模情况下，将参数信息存储在不同实验室的文件中，并将文件传递给脚本而不是单个参数。 脚本需要修改，但核心执行将是相同的。 虽然此示例使用 Azure 自动化执行 PowerShell 脚本，但还有其他选项，如在生成/发布管道中使用任务。

## <a name="next-steps"></a>后续步骤
请参阅以下文章以了解有关 Azure 自动化的更多详细信息[：Azure 自动化简介](../automation/automation-intro.md)。
