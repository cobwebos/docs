---
title: 使用警报触发 Azure 自动化 Runbook
description: 了解如何在引发 Azure 警报时触发 Runbook 运行。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e791cddb07d3204f807dbeff98b7fc69419ae23c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194030"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>使用警报触发 Azure 自动化 Runbook

可以使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) 来监视 Azure 中大多数服务的基本级别指标和日志。 可以使用[操作组](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)或经典警报调用 Azure 自动化 Runbook，以便基于警报自动执行任务。 本文介绍如何使用警报来配置和运行 Runbook。

## <a name="alert-types"></a>警报类型

可对三种警报类型使用自动化 Runbook：
* 经典指标警报
* 活动日志警报
* 准实时指标警报

当警报调用 Runbook 时，实际调用是对 Webhook 的 HTTP POST 请求。 该 POST 请求的正文包含一个 JSON 格式的对象，该对象包含与警报相关的有用属性。 下表列出了每种警报类型的有效负载架构的相应链接：

|警报  |说明|负载架构  |
|---------|---------|---------|
|[经典指标警报](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |任何平台级指标满足特定条件时，就会发送通知。 例如，VM 上的“CPU 百分比”在过去 5 分钟内大于 **90** 时。| [类指标警报有效负载架构](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[活动日志警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |当 Azure 活动日志中的任何新事件符合特定条件时，就会发送通知。 例如，当 **myProductionResourceGroup** 中出现 `Delete VM` 操作或出现状态为 **Active** 的新 Azure 服务运行状况事件时。| [活动日志警报有效负载架构](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[准实时指标警报](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |当一个或多个平台级指标满足指定条件时，就会以快于指标警报的速度发送通知。 例如，当 VM 的“CPU 百分比”大于 **90** 并且过去 5 分钟“网络传入”大于 **500 MB** 时。| [准实时指标警报有效负载架构](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

由于每种警报提供的数据不同，因此需要以不同的方式处理每种警报。 下一部分将介绍如何创建 Runbook 来处理不同类型的警报。

## <a name="create-a-runbook-to-handle-alerts"></a>创建 Runbook 以处理警报

若要对警报使用自动化，需要有一个 Runbook，其中包含用于管理传递到 Runbook 的警报 JSON 有效负载的逻辑。 下面的示例 Runbook 必须从 Azure 警报调用。 

如前面部分所述，每种警报类型都有不同的架构。 该脚本采用警报中 `WebhookData` Runbook 输入参数的 Webhook 数据。 然后，该脚本对 JSON 有效负载进行评估，确定所用的警报类型。 

此示例使用来自 VM 的警报。 它从有效负载中检索 VM 数据，然后使用该信息停止运行 VM。 必须在运行该 Runbook 的自动化帐户中建立连接。

该 Runbook 使用 **AzureRunAsConnection** [运行方式帐户](automation-create-runas-account.md)在 Azure 中进行身份验证，以便对 VM 执行管理操作。

使用此示例可以创建名为 **Stop-AzureVmInResponsetoVMAlert** 的 Runbook。 可以修改此 PowerShell 脚本，并将其用于许多不同的资源。

1. 转到 Azure 自动化帐户。
1. 在“过程自动化”下，选择“Runbook”。
1. 在 Runbook 列表的顶部选择“添加 Runbook”。 
1. 在“添加 Runbook”页上选择“快速创建”。
1. 输入 **Stop-AzureVmInResponsetoVMAlert** 作为 Runbook 名称。 对于 runbook 类型，选择“PowerShell”。 然后选择“创建”。  
1. 将以下 PowerShell 示例复制到“编辑”窗格中。 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. 选择“发布”以保存并发布 Runbook。

## <a name="create-an-action-group"></a>创建操作组

操作组是警报触发的操作集合。 Runbook 只是操作组包含的诸多操作之一。

1. 在 Azure 门户中，选择“监视” > “设置” > “操作组”。
1. 选择“添加操作组”，并输入所需的信息：  
    1. 在“操作组名称”框中输入名称。
    1. 在“短名称”框中输入名称。 使用此操作组发送通知时，短名称被用来代替完整的操作组名称。
    1. “订阅”框会自动填充当前订阅。 此订阅是在其中保存操作组的订阅。
    1. 选择在其中保存操作组的资源组。

在此示例中，将创建两项操作：Runbook 操作和通知操作。

### <a name="runbook-action"></a>Runbook 操作

在操作组中创建 Runbook 操作：

1. 在“操作”下的“操作名称”中输入操作的名称。 为“操作类型”选择“自动化 Runbook”。
1. 在“详细信息”下，选择“编辑详细信息”。  
1. 在“配置 Runbook”页上的“Runbook 源”下，选择“用户”。  
1. 选择**订阅**和**自动化帐户**，然后选择 **Stop-AzureVmInResponsetoVMAlert** Runbook。  
1. 完成后，选择“确定”。

### <a name="notification-action"></a>通知操作

在操作组中创建通知操作：

1. 在“操作”下的“操作名称”中输入操作的名称。 为“操作类型”选择“电子邮件”。  
1. 在“详细信息”下，选择“编辑详细信息”。  
1. 在“电子邮件”页上，输入要通知的电子邮件地址，选择“确定”。 除 Runbook 以外，添加电子邮件地址作为操作很有用。 这样，在 Runbook 启动时会收到通知。  

    操作组应如下图所示：

   ![“添加操作组”页](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. 若要创建操作组，请选择“确定”。

可以在创建的[活动日志警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)和[准实时警报](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)中使用此操作组。

## <a name="classic-alert"></a>经典警报

经典警报基于指标，不使用操作组。 但是，可以设置基于经典警报的 Runbook 操作。 

创建经典警报：

1. 选择“添加指标警报”。
1. 将指标警报命名为 **myVMCPUAlert**。 输入警报的简短说明。
1. 对于指标警报条件，请选择“大于”。 对于“阈值”，请选择 **10**。 对于“时段”值，请选择“过去 5 分钟”。
1. 在“采取措施”下，选择“从此警报运行 Runbook”。
1. 在“配置 Runbook”页上，选择“用户”作为“Runbook 源”。 选择自动化帐户，然后选择 **Stop-AzureVmInResponsetoVMAlert** Runbook。 选择“确定”。
1. 若要保存警报规则，请选择“确定”。

## <a name="next-steps"></a>后续步骤

* 有关使用 Webhook 启动自动化 Runbook 的详细信息，请参阅[从 Webhook 启动 Runbook](automation-webhooks.md)。
* 有关以不同方式启动 Runbook 的详细信息，请参阅[启动 Runbook](automation-starting-a-runbook.md)。
* 若要了解如何创建活动日志警报，请参阅[创建活动日志警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)。
* 若要了解如何创建准实时警报，请参阅[在 Azure 门户中创建警报规则](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)。
