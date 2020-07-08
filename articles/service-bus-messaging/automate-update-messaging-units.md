---
title: Azure 服务总线 - 自动更新消息传送单元
description: 本文介绍如何使用 Azure 自动化 runbook 自动更新服务总线命名空间的消息传送单元。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341487"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>自动更新 Azure 服务总线命名空间的消息传送单元。 
本文介绍如何根据资源（CPU 或内存）使用情况自动更新服务总线命名空间的[消息传送单元](service-bus-premium-messaging.md)。 

本文中的示例介绍如何在命名空间的 CPU 使用率超过 75% 时增加服务总线命名空间的消息传送单元。 大致步骤如下所示：

1. 使用 PowerShell 脚本创建 Azure 自动化 runbook，以便为服务总线命名空间纵向扩展（增加）消息传送单元。 
2. 在服务总线命名空间上创建 CPU 使用率警报，当命名空间 CPU 使用率超过 75% 时，将调用 PowerShell 脚本。 

> [!IMPORTANT]
> 本文仅适用于 Azure 服务总线的高级层。 


## <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间
创建高级层服务总线命名空间。 按照[在 Azure 门户中创建命名空间](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal)一文中的步骤创建命名空间。 

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户
按照[创建 Azure 自动化帐户](../automation/automation-quickstart-create-account.md)一文中的说明创建 Azure 自动化帐户。 

## <a name="import-azservice-module-from-gallery"></a>从库中导入 Az.Service 模块
将 `Az.Accounts` 和 `Az.ServiceBus` 模块从库导入到自动化帐户。 `Az.ServiceBus` 模块依赖于 `Az.Accounts` 模块，因此必须先安装它。 

有关分步说明，请参阅[从模块库导入模块](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal)。

## <a name="create-and-publish-a-powershell-runbook"></a>创建并发布 PowerShell runbook

1. 按照[创建 PowerShell runbook](../automation/automation-quickstart-create-runbook.md) 一文中的说明创建 PowerShell runbook。 

    下面是一个示例 PowerShell 脚本，可用于为服务总线命名空间增加消息传送单元。 自动化 runbook 中的此 PowerShell 脚本将 MU 从 1 增至 2、从 2 增至 4 或从 4 增至 8。 此属性的允许值为：1、2、4 和 8. 你可以创建另一个 runbook 来减少消息传送单元。

    namespaceName 和 resourceGroupName 参数用于独立于警报场景测试脚本 。 
    
    WebHookData 参数用于警报在运行时传递资源组名称、资源名称等信息。 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. 通过指定 namespaceName 和 resourceGroupName 参数的值来[测试工作簿](../automation/manage-runbooks.md#test-a-runbook) 。 确认已更新命名空间上的消息传送单元。 
3. 测试成功后，请[发布工作簿](..//automation/manage-runbooks.md#publish-a-runbook)，以便稍后可将其添加为命名空间上的警报的操作。 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>在命名空间上创建警报以触发 runbook
请参阅[使用警报触发 Azure 自动化 runbook](../automation/automation-create-alert-triggered-runbook.md)一文，以便在服务总线命名空间上配置警报，以触发你创建的自动化 runbook。 例如，可以针对每个命名空间的 CPU 使用率或每个命名空间的内存大小使用情况指标创建警报，并添加操作以触发创建的自动化 runbook 。 有关这些指标的详细信息，请参阅[资源使用情况指标](service-bus-metrics-azure-monitor.md#resource-usage-metrics)。

以下过程显示了如何创建警报，该警报将在命名空间 CPU 使用率超过 75% 时触发自动化 runbook 。

1. 在命名空间的“服务总线命名空间”页上，在左侧菜单上选择“警报”，然后在工具栏上选择“+新建警报规则”  。 
    
    ![“警报”页 -“新建警报规则”按钮](./media/automate-update-messaging-units/alerts-page.png)
2. 在“创建警报规则”页上，单击“选择条件” 。 

    ![“创建警报规则”页 - 选择条件](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. 在“配置信号逻辑”页上，为信号选择“CPU” 。 

    ![配置信号逻辑 - 选择 CPU](./media/automate-update-messaging-units/configure-signal-logic.png)
4. 输入“阈值”（本例为 75%），然后选择“完成”  。 

    ![配置 CPU 信号](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. 现在，在“创建警报”页上，单击“选择操作组” 。
    
    ![选择操作组](./media/automate-update-messaging-units/select-action-group-button.png)
6. 在工具栏上选择“创建操作组”按钮。 

    ![“创建操作组”按钮](./media/automate-update-messaging-units/create-action-group-button.png)
7. 在“添加操作组”页上执行以下步骤：
    1. 为操作组输入“名称”。 
    2. 为操作组输入“短名称”。
    3. 选择要在其中创建此操作组的“订阅”。
    4. 选择“资源组”。 
    5. 在“操作”部分，输入“操作的名称”，并为“操作类型”选择“自动化 Runbook”   。 

        ![“添加操作组”页](./media/automate-update-messaging-units/add-action-group-page.png)
8. 在“配置 Runbook”页上执行以下步骤：
    1. 选择“用户”作为“Runbook 源” 。 
    2. 对于“订阅”，请选择包含自动化帐户的 Azure 订阅 。 
    3. 对于“自动化帐户”，请选择你的自动化帐户 。
    4. 对于“Runbook”，请选择你的 runbook。 
    5. 在“配置 Runbook”页上选择“确定” 。 
        ![配置 runbook](./media/automate-update-messaging-units/configure-runbook.png)
9. 在“添加操作组”页面上选择“确定” 。 
5. 现在，在“创建警报规则”页上输入“规则的名称”，然后选择“创建警报规则”  。 
    ![创建警报规则](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > 现在，当命名空间 CPU 使用率超过 75 时，警报会触发自动化 runbook，这会增加服务总线命名空间的消息传送单元。 同样，可以为另一个自动化 runbook 创建警报，如果命名空间 CPU 使用率低于 25，则会减少消息传送单元。 

## <a name="next-steps"></a>后续步骤
若要了解消息传送单元，请参阅[高级消息传送](service-bus-premium-messaging.md)
