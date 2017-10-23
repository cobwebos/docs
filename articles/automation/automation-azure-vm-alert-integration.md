---
title: " 使用自动化 Runbook 针对 Azure VM 警报做出补救 | Microsoft Docs"
description: "本文演示如何将 Azure 虚拟机警报与 Azure 自动化 Runbook 集成，以及如何自动补救问题"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: 18cccc88ab74235722e2f4886671fc483ab67da8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Azure 自动化解决方案 - 针对 Azure VM 警报做出补救
Azure 自动化和 Azure 虚拟机发布了一项新功能，可让你将虚拟机 (VM) 配置为在出现警报时运行自动化 Runbook。 这项新功能可让你自动执行标准补救措施来响应 VM 警报，例如重新启动或停止 VM。

以前，在创建 VM 警报规则期间，可以在某个 Runbook 中[指定自动化 Webhook](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/)，以便在触发警报时运行该 Runbook。 但是，这需要创建 Runbook，为 Runbook 创建 Webhook，在创建警报规则期间复制并粘贴 Webhook。 使用新版本时，该过程将轻松得多，因为可以在创建警报规则期间直接从列表中选择 Runbook，并且可以选择要运行 Runbook 的自动化帐户，或轻松创建一个帐户。

在本文中，我们将说明如何轻松设置 Azure VM 警报，以及配置为每当触发警报就运行自动化 Runbook。 示例方案包括：当由于 VM 上的应用程序出现内存泄漏而导致内存使用量超出某个阈值时重新启动 VM，或者当 CPU 用户时间在过去一小时低于 1% 以及未使用时停止 VM。 我们还将说明在自动化帐户中自动创建服务主体如何能够在 Azure 警报补救措施中简化 Runbook 的使用。

## <a name="create-an-alert-on-a-vm"></a>在 VM 上创建警报
执行以下步骤，将警报配置为在达到 VM 阈值时启动 Runbook。

> [!NOTE]
> 此版本仅支持 V2 虚拟机，我们即将添加对经典 VM 的支持。  
> 
> 

1. 登录到 Azure 门户，并单击“虚拟机”。  
2. 选择一个虚拟机。  
3. 在 VM 屏幕上，在“监视”部分中，单击“警报规则”。
4. 在“警报规则”窗格中，单击“添加警报”。

这将打开“添加警报规则”页面，可以在其中配置警报条件，并选择下列一个或所有选项：向某人发送电子邮件、使用 Webhook 将警报转发到另一个系统，和/或作为响应运行自动化 Runbook 以尝试补救问题。

## <a name="configure-a-runbook"></a>配置 Runbook
要将 Runbook 配置为在达到 VM 警报阈值时运行，请选择“自动化 Runbook”。 在“配置 Runbook”窗格中，可以选择要运行的 Runbook，以及运行 Runbook 时所在的自动化帐户。

![配置自动化 Runbook 和创建新的自动化帐户](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> 在此版本中，可以从服务提供的三个 Runbook 中选择 –“重新启动 VM”、“停止 VM”或“删除 VM”（删除它）。  将来的版本可让你选择其他 Runbook 或自己的某个 Runbook。
> 
> 

![可供选择的 Runbook](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

在三个可用的 Runbook 中选择一个后，会显示“自动化帐户”下拉列表，可以选择用于运行 Runbook 的自动化帐户。 Runbook 需在 Azure 订阅中的[自动化帐户](automation-security-overview.md)上下文中运行。 可以选择已创建的自动化帐户，也可以自行创建新的自动化帐户。

提供的 Runbook 将使用服务主体在 Azure 中进行身份验证。 如果选择在某个现有的自动化帐户中运行 Runbook，我们会自动为你创建服务主体。 如果选择创建新的自动化帐户，则我们会自动创建帐户和服务主体。 在这两种情况下，还会在自动化帐户中创建两个资产 – 一个名为 **AzureRunAsCertificate** 的证书资产和一个名为 **AzureRunAsConnection** 的连接资产。 Runbook 使用 **AzureRunAsConnection** 在 Azure 中进行身份验证，以便对 VM 执行管理操作。

> [!NOTE]
> 在订阅范围内创建了服务主体，并向其分配了“参与者”角色。 需要分配此角色，以便帐户有权运行自动化 Runbook 来管理 Azure VM。  创建自动化帐户和/或服务主体是一次性的事件。 创建后，可以使用该帐户来对其他 Azure VM 警报运行 Runbook。
> 
> 

单击“确定”时，会配置警报；如果选择创建新自动化帐户的选项，则将连同服务主体一起创建帐户。  只需几秒钟即可完成此过程。  

![正在配置的 Runbook](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

配置完成后，可以在“添加警报规则”页面中看到 Runbook 的名称。

![已配置的 Runbook](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

在“添加警报规则”页面中，单击“确定”。  如果虚拟机处于运行状态，则会创建并激活警报规则。

### <a name="enable-or-disable-a-runbook"></a>启用或禁用 Runbook
如果针对警报配置了 Runbook，无需删除 Runbook 配置即可将它禁用。 这样，便可以在保持警报运行状态的同时测试某些警报规则，再重新启用 Runbook。

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>创建可与 Azure 警报配合使用的 runbook
选择一个 runbook 作为 Azure 警报规则的一部分时，该 runbook 自身需要有逻辑来管理传递给它的警报数据。  如果在警报规则中配置了 runbook，将为该 runbook 创建 webhook；之后每次触发警报时会使用该 webhook 启动 runbook。  对启动 runbook 的实际调用是对 webhook URL 的 HTTP POST 请求。 该 POST 请求的正文包含一个 JSON 格式的对象，该对象包含与警报相关的有用属性。  如下所示，警报数据包含 subscriptionID、resourceGroupName、resourceName 和 resourceType 等详细信息。

### <a name="example-of-alert-data"></a>警报数据示例
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

当自动化 webhook 服务收到 HTTP POST，它将提取警报数据，并将数据传递给 WebhookData runbook 输入参数中的 runbook。  下面是一个示例 runbook，演示了如何使用 WebhookData 参数和提取警报数据，以及将数据用于管理触发警报的 Azure 资源。

### <a name="example-runbook"></a>示例 runbook
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>摘要
现在，在 Azure VM 上配置警报时，可以轻松地配置自动化 Runbook，以便在触发警报时自动执行补救措施。 在此版本中，可以根据警报情况，选择通过 Runbook 重新启动、停止或删除 VM。 然后，便可以实施不同的方案，包括对警报触发时自动执行的措施（通知、故障排除、补救）进行控制。

## <a name="next-steps"></a>后续步骤
* 若要开始使用图形 Runbook，请参阅 [我的第一个图形 Runbook](automation-first-runbook-graphical.md)
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)
* 若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)

