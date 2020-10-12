---
title: 使用警报触发 Azure 自动化 Runbook
description: 本文介绍如何在引发 Azure 警报时触发 runbook 运行。
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: acf31af6d3ba3d78a6435210fa17562aaddac0a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186599"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>使用警报触发 Azure 自动化 Runbook

可以使用 [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) 来监视 Azure 中大多数服务的基本级别指标和日志。 可以使用[操作组](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)或经典警报调用 Azure 自动化 Runbook，以便基于警报自动执行任务。 本文介绍如何使用警报来配置和运行 Runbook。

## <a name="alert-types"></a>警报类型

可对三种警报类型使用自动化 Runbook：

* 常见警报
* 活动日志警报
* 近实时指标警报

> [!NOTE]
> 常见的警报架构会在 Azure 中标准化警报通知的使用体验。 如今，Azure 中的三种警报类型（指标、日志和活动日志）都已有自己的电子邮件模板、Webhook 架构等。若要了解详细信息，请参阅[常见警报架构](../azure-monitor/platform/alerts-common-schema.md)

当警报调用 Runbook 时，实际调用是对 Webhook 的 HTTP POST 请求。 该 POST 请求的正文包含一个 JSON 格式的对象，该对象包含与警报相关的有用属性。 下表列出了每种警报类型的有效负载架构的相应链接：

|警报  |说明|负载架构  |
|---------|---------|---------|
|[常见警报](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|常见警报架构，用于在目前的 Azure 中标准化警报通知的使用体验。|常见警报有效负载架构|
|[活动日志警报](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |当 Azure 活动日志中的任何新事件符合特定条件时，就会发送通知。 例如，当 myProductionResourceGroup 中出现 `Delete VM` 操作或出现状态为 Active 的新 Azure 服务运行状况事件时。| [活动日志警报有效负载架构](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[准实时指标警报](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |当一个或多个平台级指标满足指定条件时，就会以快于指标警报的速度发送通知。 例如，当 VM 的“CPU 百分比”大于 90 并且过去 5 分钟“网络传入”大于 500 MB 时。 | [准实时指标警报有效负载架构](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

由于每种警报提供的数据不同，因此需要以不同的方式处理每种警报。 下一部分将介绍如何创建 Runbook 来处理不同类型的警报。

## <a name="create-a-runbook-to-handle-alerts"></a>创建 Runbook 以处理警报

若要对警报使用自动化，需要有一个 Runbook，其中包含用于管理传递到 Runbook 的警报 JSON 有效负载的逻辑。 下面的示例 Runbook 必须从 Azure 警报调用。

如前面部分所述，每种警报类型都有不同的架构。 此脚本采用从 `WebhookData` runbook 输入参数中的警报获取的 Webhook 数据。 然后，该脚本对 JSON 有效负载进行评估，确定正在使用的警报类型。

此示例使用来自 VM 的警报。 它从有效负载中检索 VM 数据，然后使用该信息停止运行 VM。 必须在运行该 Runbook 的自动化帐户中建立连接。 使用警报触发 runbook 时，必须检查触发的 runbook 中的警报状态。 每次警报更改状态时，都会触发 runbook。 警报有多个状态，其中两个最常见的状态为“已激活”和“已解决”。 检查 runbook 逻辑的状态，以确保 runbook 不会运行多次。 本文中的示例仅演示了如何查找状态为“已激活”的警报。

该 runbook 使用连接资产 `AzureRunAsConnection` [运行方式帐户](./manage-runas-account.md)在 Azure 中进行身份验证，以便对 VM 执行管理操作。

使用此示例可以创建名为 **Stop-AzureVmInResponsetoVMAlert** 的 Runbook。 可以修改此 PowerShell 脚本，并将其用于许多不同的资源。

1. 转到 Azure 自动化帐户。
2. 在“过程自动化”下，选择“Runbook”。 
3. 在 Runbook 列表的顶部选择“+ 创建 Runbook”。
4. 在“添加 runbook”页上，输入 Stop-AzureVmInResponsetoVMAlert 作为 runbook 名称 。 对于 runbook 类型，选择“PowerShell”。 然后选择“创建”。  
5. 将以下 PowerShell 示例复制到“编辑”页中。

    ```powershell-interactive
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
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

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
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. 选择“发布”以保存并发布 Runbook。

## <a name="create-the-alert"></a>创建警报

警报使用操作组，操作组是警报触发的操作的集合。 Runbook 只是操作组包含的诸多操作之一。

1. 在自动化帐户中，选择“监视”下的“警报” 。
1. 选择“+ 新建警报规则”。
1. 单击“资源”下的“选择” 。 在“选择资源”页上，选择要发出其关闭警报的 VM，然后单击“完成”"。
1. 在“条件”下单击“添加条件” 。 选择要使用的信号（例如“CPU 百分比”），然后单击“完成” "。
1. 在“配置信号逻辑”页上，在“警报逻辑”下输入“阈值”，然后单击“完成”   。
1. 在“操作组”下，选择“新建” 。
1. 在“添加操作组”页上，为操作组指定名称和短名称。
1. 为操作指定一个名称。 对于操作类型，选择“自动化 runbook”。
1. 选择“编辑详细信息”。 在“配置 Runbook”页上的“Runbook 源”下，选择“用户” 。  
1. 选择**订阅**和**自动化帐户**，然后选择 **Stop-AzureVmInResponsetoVMAlert** Runbook。  
1. 为“启用常见警报架构”选择“是” 。
1. 若要创建操作组，请选择“确定”。

    ![“添加操作组”页](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    可以在创建的[活动日志警报](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)和[准实时警报](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json)中使用此操作组。

1. 在“警报详细信息”下，添加警报规则名称和说明，然后单击“创建警报规则” 。

## <a name="next-steps"></a>后续步骤

* 若要使用 Webhook 启动 runbook，请参阅 [从 Webhook 启动 runbook](automation-webhooks.md)。
* 如需了解启动 runbook 的各种方式，请参阅[启动 runbook](./start-runbooks.md)。
* 若要创建活动日志警报，请参阅[创建活动日志警报](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)。
* 若要了解如何创建准实时警报，请参阅[在 Azure 门户中创建警报规则](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
