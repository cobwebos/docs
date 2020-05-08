---
title: 使用 PowerShell 管理 Azure 服务总线资源 | Microsoft Docs
description: 本文介绍如何使用 Azure PowerShell 模块创建和管理服务总线实体（命名空间、队列、主题和订阅）。
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8605e269f09e4fedf7a61817a3634e75081593ca
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890236"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>使用 PowerShell 管理服务总线资源

Microsoft Azure PowerShell 是一个脚本编写环境，可用于控制和自动执行 Azure 服务的部署和管理。 本文介绍如何使用[服务总线 Resource Manager PowerShell 模块](/powershell/module/az.servicebus)，通过本地 Azure PowerShell 控制台或脚本来预配和管理服务总线实体（命名空间、队列、主题和订阅）。

还可以使用 Azure 资源管理器模板管理服务总线实体。 有关详细信息，请参阅[使用 Azure 资源管理器模板创建服务总线资源](service-bus-resource-manager-overview.md)一文。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备条件

在开始之前，需要符合以下先决条件：

* Azure 订阅。 有关获取订阅的详细信息，请参阅[购买选项][purchase options]、[会员套餐][member offers]或[免费帐户][free account]。
* 配备 Azure PowerShell 的计算机。 有关说明，请参阅 [Azure PowerShell cmdlet 入门](/powershell/azure/get-started-azureps)。
* 大致了解 PowerShell 脚本、NuGet 包和 .NET Framework。

## <a name="get-started"></a>入门

第一步是使用 PowerShell 登录 Azure 帐户和 Azure 订阅。 按照 [Azure PowerShell cmdlet 入门](/powershell/azure/get-started-azureps)中的说明登录 Azure 帐户，检索并访问 Azure 订阅中的资源。

## <a name="provision-a-service-bus-namespace"></a>设置 Service Bus 命名空间

使用服务总线命名空间时，可以使用 [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace)、[New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace)、[Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace) 和 [Set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) cmdlet。

本示例在脚本中创建几个本地变量：`$Namespace` 和 `$Location`。

* `$Namespace` 是我们要使用的服务总线命名空间的名称。
* `$Location` 标识我们要在其中预配命名空间的数据中心。
* `$CurrentNamespace` 将存储我们检索（或创建）的引用命名空间。

在实际脚本中，`$Namespace` 和 `$Location` 可作为参数传递。

此部分脚本执行以下任务：

1. 尝试使用指定名称检索服务总线命名空间。
2. 如果找到该命名空间，则报告它找到的内容。
3. 如果找不到该命名空间，则会创建该命名空间，并检索新创建的命名空间。
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>创建命名空间授权规则

下面的示例演示如何使用 [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule)、[Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule)、[Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule) 和 [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) cmdlet 管理命名空间授权规则。

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>创建队列

若要创建队列或主题，请使用上一部分中的脚本执行命名空间检查。 然后，创建队列：

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>修改队列属性

执行上一部分中的脚本后，可以使用 [Set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) cmdlet 更新队列的属性，如以下示例所示：

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>设置其他 Service Bus 实体

可以使用[服务总线 PowerShell 模块](/powershell/module/az.servicebus)预配其他实体，例如主题和订阅。 这些 cmdlet 在语法上与上一部分所示的队列创建 cmdlet 类似。

## <a name="next-steps"></a>后续步骤

- 有关服务总线 Resource Manager PowerShell 模块的完整文档，请参阅[此处](/powershell/module/az.servicebus)。 此页列出所有可用的 cmdlet。
- 有关使用 Azure 资源管理器模板的信息，请参阅[使用 Azure 资源管理器模板创建服务总线资源](service-bus-resource-manager-overview.md)一文。
- 有关[服务总线 .NET 管理库](service-bus-management-libraries.md)的信息。

这些博客文章介绍管理服务总线实体的一些备选方法：

* [How to create Service Bus queues, topics and subscriptions using a PowerShell script](https://docs.microsoft.com/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)（如何使用 PowerShell 脚本创建服务总线队列、主题和订阅）
* [How to create a Service Bus Namespace and an Event Hub using a PowerShell script](https://docs.microsoft.com/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)（如何使用 PowerShell 脚本创建服务总线命名空间和事件中心）
* [服务总线 PowerShell 脚本](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
