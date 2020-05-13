---
title: Azure 虚拟机规模集实例的终止通知
description: 了解如何为 Azure 虚拟机规模集实例启用终止通知
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 695fd03d7c1856ad39b7672d826f85bc4c68a99c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125173"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Azure 虚拟机规模集实例的终止通知
规模集实例可以选择接收实例终止通知，并将预定义的延迟超时设置为终止操作。 终止通知通过 Azure Metadata Service 发送– [Scheduled Events](../virtual-machines/windows/scheduled-events.md)，它提供有影响力操作（如重新启动和重新部署）的通知。 解决方案将另一个事件（终止–）添加到 Scheduled Events 列表，而终止事件的关联延迟将取决于其规模集模型配置中用户指定的延迟限制。

向功能注册后，规模集实例无需等待指定的超时过期就会被删除。 接收到终止通知后，在终止超时过期之前，可以选择随时删除该实例。

## <a name="enable-terminate-notifications"></a>启用终止通知
可以通过多种方法在规模集实例上启用终止通知，如以下示例中所述。

### <a name="azure-portal"></a>Azure 门户

在创建新的规模集时，以下步骤启用了终止通知。 

1. 请参阅**虚拟机规模集**。
1. 选择 " **+ 添加**" 创建新的规模集。
1. 请参阅 "**管理**" 选项卡。 
1. 找到 "**实例终止**" 部分。
1. 对于**实例终止通知**，请选择 **"打开"**。
1. 对于 "**终止延迟（分钟）**"，设置所需的默认超时值。
1. 创建新规模集后，请选择 "**查看 + 创建**" 按钮。 

> [!NOTE]
> 无法在 Azure 门户中的现有规模集上设置终止通知

### <a name="rest-api"></a>REST API

下面的示例对规模集模型启用了终止通知。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

对于规模集中所有实例的任何 "终止" 操作，上述块指定了5分钟的超时延迟（如*PT5M*所示）。 字段*notBeforeTimeout*可以采用 ISO 8601 格式介于5到15分钟之间的任何值。 可以通过修改上述*terminateNotificationProfile*下的*notBeforeTimeout*属性来更改终止操作的默认超时。

对规模集模型启用*scheduledEventsProfile*并设置*notBeforeTimeout*后，将各个实例更新为[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映所做的更改。

> [!NOTE]
>仅可通过 API 版本2019-03-01 及更高版本启用规模集实例上的终止通知

### <a name="azure-powershell"></a>Azure PowerShell
创建新规模集时，可以使用[AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 在规模集上启用终止通知。

此示例脚本演示如何使用配置文件创建规模集和关联的资源：[创建一个完整的虚拟机规模集](./scripts/powershell-sample-create-complete-scale-set.md)。 可以通过将参数*TerminateScheduledEvents*和*TerminateScheduledEventNotBeforeTimeoutInMinutes*添加到用于创建规模集的配置对象来提供配置终止通知。 下面的示例启用此功能，延迟超时为10分钟。

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

使用[AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 在现有规模集上启用终止通知。

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
上述示例在现有规模集上启用了终止通知，并为终止事件设置了15分钟的超时时间。

在规模集模型中启用计划事件并设置超时后，将各个实例更新为[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映所做的更改。

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下示例用于在创建新规模集时启用终止通知。

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

上面的示例首先创建一个资源组，然后创建一个新的规模集，该规模集具有启用了终止通知的默认超时值。

以下示例用于在现有规模集中启用终止通知。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>获取终止通知

终止通知通过[Scheduled Events](../virtual-machines/windows/scheduled-events.md)提供，这是一个 Azure 元数据服务。 Azure 元数据服务使用可从 VM 内访问的 REST 终结点公开有关正在运行的虚拟机的信息。 此信息通过不可路由的 IP 提供，因此不会在 VM 外部公开。

首次请求事件时，将为规模集启用 Scheduled Events。 第一次调用时，可能会出现延迟的响应，最长为两分钟。 定期查询终结点以检测即将发生的维护事件和正在进行的维护活动的状态。

如果规模集实例不发出24小时的请求，则为规模集禁用 Scheduled Events。

### <a name="endpoint-discovery"></a>终结点发现
对于启用 VNET 的 Vm，元数据服务可从静态不可路由的 IP 169.254.169.254 获得。

最新版本的计划事件的完整终结点是：
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>查询响应
响应包含计划事件的数组。 空数组表示目前没有计划事件。

如果有计划事件，响应会包含事件的数组。 对于 "终止" 事件，响应将如下所示：
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentIncarnation*是一个 ETag，它提供了一种简单的方法来检查事件负载自上次查询后是否已更改。

有关上述每个字段的详细信息，请参阅适用于[Windows](../virtual-machines/windows/scheduled-events.md#event-properties)和[Linux](../virtual-machines/linux/scheduled-events.md#event-properties)的 Scheduled Events 文档。

### <a name="respond-to-events"></a>响应事件
当你了解到即将发生的事件并完成正常关闭的逻辑后，你可以通过向包含 EventId 的元数据服务发出 POST 调用来批准未完成的事件。 POST 调用向 Azure 指出，它可以继续 VM 删除。

下面是 POST 请求正文中应有的 json。 请求应包含 StartRequests 的列表。 每个 StartRequest 都包含您要加速的事件的 EventId：
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

确保规模集中的每个 VM 仅批准与该 VM 相关的 EventID。 VM 可以[通过实例元数据](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)获取其自己的 vm 名称。 此名称采用 "{scale-name} _ {instance-id}" 格式，并将显示在上述查询响应的 "资源" 部分中。

你还可以参阅使用[PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample)和[Python](../virtual-machines/linux/scheduled-events.md#python-sample)查询和响应事件的示例脚本。

## <a name="tips-and-best-practices"></a>提示和最佳实践
-   仅在 "删除" 操作上终止通知–如果规模集已启用*scheduledEventsProfile* ，则所有删除操作（手动删除或自动缩放启动的扩展）将生成终止事件。 其他操作（例如重新启动、重置映像、重新部署和停止/解除分配）不会生成终止事件。 不能为低优先级 Vm 启用终止通知。
-   无强制等待超时–您可以在收到事件之后、事件的*NotBefore*时间到期之前随时开始终止操作。
-   在超时时必需删除–生成事件后，不能扩展超时值。 超时到期后，将处理挂起的终止事件，并删除 VM。
-   可修改超时值–可以在删除实例之前随时修改超时值，方法是修改规模集模型中的*notBeforeTimeout*属性，并将 VM 实例更新到最新模型。
-   批准所有挂起的删除-如果 VM_1 上有一个挂起的删除未批准，并且你已在 VM_2 上批准了另一个终止事件，则在 VM_1 的 "终止" 事件被批准或已过超时之前，不会删除 VM_2。 批准 VM_1 的终止事件之后，将删除 VM_1 和 VM_2。
-   批准所有同时删除–扩展上面的示例，如果 VM_1 和 VM_2 具有相同的*NotBefore*时间，则必须批准两个终止事件，否则在超时过期之前，都不会删除 VM。

## <a name="troubleshoot"></a>疑难解答
### <a name="failure-to-enable-scheduledeventsprofile"></a>未能启用 scheduledEventsProfile
如果在 "VirtualMachineProfile" 类型的对象上出现 "BadRequest" 错误，并显示一条错误消息，指出 "找不到成员 ' scheduledEventsProfile '"，请检查用于规模集操作的 API 版本。 需要计算 API 版本**2019-03-01**或更高版本。 

### <a name="failure-to-get-terminate-events"></a>未能获取终止事件
如果未通过 Scheduled Events 获取任何**终止**事件，请检查用于获取事件的 API 版本。 终止事件需要元数据服务 API 版本**2019-01-01**或更高版本。
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>用不正确的 NotBefore 时间获取终止事件  
对规模集模型启用*scheduledEventsProfile*并设置*notBeforeTimeout*后，将各个实例更新为[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映所做的更改。

## <a name="next-steps"></a>后续步骤
了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
