---
title: "使用自动化 Runbook 响应 Azure 警报 | Microsoft Docs"
description: "了解如何在引发 Azure 警报时触发 Runbook 运行。"
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>使用自动化 Runbook 响应 Azure 警报

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) 针对 Microsoft Azure 中的大多数服务提供基本级别的指标和日志。 可以使用[操作组](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)或从经典警报调用 Azure 自动化 Runbook，以便基于警报自动执行任务。 本文介绍如何基于以下警报配置和运行 Runbook。

## <a name="alert-types"></a>警报类型

所有三种类型的警报均支持 Runbook 操作。 当警报调用 Runbook 时，实际调用是对 Webhook 的 HTTP POST 请求。 该 POST 请求的正文包含一个 JSON 格式的对象，该对象包含与警报相关的有用属性。 下表包含每种警报类型的有效负载架构的相应链接：

|警报  |说明|有效负载架构  |
|---------|---------|---------|
|[经典指标警报](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |任何平台级指标满足特定条件时（例如，VM 上的 CPU % 在过去 5 分钟内大于 90），就会收到通知。| [有效负载架构](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[活动日志警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |当 Azure 活动日志中的任何新事件符合特定条件时（例如，myProductionResourceGroup 中出现“删除 VM”操作或出现状态为“Active”的新服务运行状况事件时）就会收到通知。| [有效负载架构](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[准实时指标警报](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |当一个或多个平台级指标满足指定条件时（例如，VM 的 CPU 百分比大于 90 并且过去 5 分钟网络传入大于 500 MB ），就会收到通知，速度比指标警报更快。| [有效负载架构](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

由于每个警报提供的数据都是不同的，因此需要以不同方式处理每个警报。 在下一部分中，将了解如何创建 Runbook 来处理这些不同类型的警报。

## <a name="create-a-runbook-to-handle-alerts"></a>创建 Runbook 以处理警报

若要对警报使用自动化，需要有一个 Runbook，其中包含用于管理传递到 Runbook 的警报 JSON 有效负载的逻辑。 下面的示例 Runbook 必须从 Azure 警报调用。 如前面部分所述，每种警报类型都有不同的架构。 该脚本采用警报中 `WebhookData` Runbook 输入参数的 Webhook 数据，并对 JSON 有效负载进行评估，确定所使用的警报类型。 以下示例将用于来自 VM 的警报。 它从有效负载中检索 VM 数据，并使用该信息停止运行 VM。 必须在运行该 Runbook 的自动化帐户中建立连接。

该 Runbook 使用 **AzureRunAsConnection** [运行方式帐户](automation-create-runas-account.md)在 Azure 中进行身份验证，以便对 VM 执行管理操作。

以下 PowerShell 脚本经更改后可用于许多不同的资源。

使用以下示例，通过示例 PowerShell 创建一个名为 **Stop-AzureVmInResponsetoVMAlert** 的 Runbook。

1. 打开自动化帐户。
1. 单击“流程自动化”下的“Runbook”。 此时会显示 Runbook 列表。
1. 单击列表顶部的“添加 Runbook”按钮。 在“添加 Runbook”页上选择“快速创建”。
1. 输入“Stop-AzureVmInResponsetoVMAlert”作为 Runbook 的“名称”，选择“PowerShell”作为“Runbook 类型”。 单击“创建”。
1. 将以下 PowerShell 示例复制到编辑窗格中。 单击“发布”以保存并发布 Runbook。

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

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
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
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
        # Schema not supported
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

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
            Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
            $ConnectionAssetName = "AzureRunAsConnection"
            Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Verbose "Authenticating to Azure with service principal." -Verbose
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>创建操作组

操作组是指基于警报采取的一系列操作。 Runbook 只是操作组包含的诸多操作之一。

1. 在门户中，选择“监视”。

1. 在“设置”下选择“操作组”。

1. 选择“添加操作组”，并填写字段。

1. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

1. “订阅”框会自动填充当前订阅。 此“订阅”是在其中保存操作组的订阅。
选择在其中保存操作组的“资源组”。

在此示例中，将创建两项操作：Runbook 操作和通知操作。

### <a name="runbook-action"></a>Runbook 操作

以下步骤可在操作组中创建 Runbook 操作。

1. 在“操作”下，为操作指定一个名称。

1. 选择“自动化 Runbook”作为“操作类型”。

1. 在“详细信息”下，选择“编辑详细信息”

1. 在“配置 Runbook”页上，选择“Runbook 源”下的“用户”。

1. 选择“订阅”和“自动化帐户”，最后选择在前面步骤中创建的名为“Stop-AzureVmInResponsetoVMAlert”的 Runbook。

1. 完成后单击“确定”。

### <a name="notification-action"></a>通知操作

以下步骤可在操作组中创建通知操作。

1. 在“操作”下，为操作指定一个名称。

1. 选择“电子邮件”作为“操作类型”。

1. 在“详细信息”下，选择“编辑详细信息”

1. 在“电子邮件”页上，输入要通知的电子邮件地址，单击“确定”。 像添加 Runbook 一样添加电子邮件地址作为一项操作很有用，因为用户可在 Runbook 启动时收到通知。 操作组应如下图所示：

   ![“添加操作组”页](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. 单击“确定”创建操作组。

创建操作组之后，就可以创建[活动日志警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)或[准实时警报](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)，并使用所创建的操作组。

## <a name="classic-alert"></a>经典警报

经典警报基于指标，不使用操作组，但具有以它们为基础的 Runbook 操作。 若要创建经典警报，请使用以下步骤：

1. 选择“+ 添加指标警报”。

1. 将指标警报命名为“myVMCPUAlert”，并为警报提供简短介绍。

1. 将指标警报的“条件”设置为“大于”，将“阈值”设置为“10”，并将“时段”设置为“过去 5 分钟”。

1. 在“采取措施”下，选择“从此警报运行 Runbook”

1. 在“配置 Runbook”页上，选择“用户”作为“Runbook 源”。 选择自动化帐户，并选择 **Stop-AzureVmInResponsetoVMAlert** Runbook。 单击“确定”，然后再次单击“确定”以保存警报规则。

## <a name="next-steps"></a>后续步骤

* 有关使用 Webhook 启动自动化 Runbook 的详细信息，请参阅[从 Webhook 启动 Runbook](automation-webhooks.md)
* 有关以不同方式启动 Runbook 的详细信息，请参阅 [启动 Runbook](automation-starting-a-runbook.md)
* 若要了解如何创建活动日志警报，请参阅[创建活动日志警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)
* 若要了解如何创建准实时警报，请访问[使用 Azure 门户创建警报规则](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)。