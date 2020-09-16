---
title: 为 Azure 虚拟机规模集实例终止通知
description: 了解如何为 Azure 虚拟机规模集实例启用终止通知
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: d4b31eb59ed0bae2afe408546ece66eacade9ddb
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603826"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>为 Azure 虚拟机规模集实例终止通知
规模集实例可以选择接收实例终止通知，并为终止操作设置预定义的延迟超时。 终止通知通过 Azure Metadata Service 发送– [Scheduled Events](../virtual-machines/windows/scheduled-events.md)，它提供有影响力操作（如重新启动和重新部署）的通知。 解决方案将另一个事件（终止–）添加到 Scheduled Events 列表，而终止事件的关联延迟将取决于其规模集模型配置中用户指定的延迟限制。

注册该功能后，规模集实例无需等待指定的超时过期就会被删除。 收到终止通知后，实例可以选择在终止超时到期之前随时删除。

## <a name="enable-terminate-notifications"></a>启用终止通知
可以通过多种方式在规模集实例上启用终止通知，详见以下示例。

### <a name="azure-portal"></a>Azure 门户

创建新的规模集时，可以通过以下步骤启用终止通知。 

1. 转到“虚拟机规模集”。
1. 选择“+ 添加”，创建新的规模集。
1. 转到“管理”选项卡。 
1. 找到“实例终止”部分。
1. 对于“实例终止通知”，选择“启用” 。
1. 对于“终止延迟(分钟)”，设置所需的默认超时值。
1. 创建完新的规模集后，选择“查看 + 创建”按钮。 

> [!NOTE]
> 无法对 Azure 门户中的现有规模集设置终止通知

### <a name="rest-api"></a>REST API

以下示例对规模集模型启用终止通知。

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

上面的代码块为规模集中所有实例的任何终止操作指定 5 分钟的超时延迟（如 PT5M 所示）。 字段 notBeforeTimeout 可以接受采用 ISO 8601 格式，在 5 到 15 分钟之间的任何值。 可以通过修改上文所述的 terminateNotificationProfile 下的 notBeforeTimeout 属性来更改终止操作的默认超时 。

对规模集模型启用 scheduledEventsProfile 并设置 notBeforeTimeout 后，将各个实例更新为[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改 。

> [!NOTE]
>只能使用 API 版本 2019-03-01 及更高版本对规模集实例启用终止通知

### <a name="azure-powershell"></a>Azure PowerShell
创建新的规模集时，可以使用[ New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 对规模集启用终止通知。

此示例脚本使用配置文件演示如何创建模集和相关资源：[创建完整的虚拟机规模集](./scripts/powershell-sample-create-complete-scale-set.md)。 通过将参数 TerminateScheduledEvents 和 TerminateScheduledEventNotBeforeTimeoutInMinutes 添加到用于创建规模集的配置对象来配置终止通知 。 以下示例以 10 分钟的延迟超时启用这项功能。

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 对现有规模集启用终止通知。

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
上述示例对现有规模集启用终止通知，并为终止事件设置 15 分钟的超时。

对规模集模型启用计划事件并设置超时后，将各个实例更新为[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改。

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

上面的示例首先创建一个资源组，然后创建一个新的规模集，并启用了 10 分钟的默认超时终止通知。

以下示例用于在现有规模集中启用终止通知。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>获取终止通知

终止通知通过 [Scheduled Events](../virtual-machines/windows/scheduled-events.md) 传递，它是一项 Azure 元数据服务。 Azure 元数据服务公开在 VM 中使用可访问的 REST 终结点运行虚拟机的相关信息。 该信息通过不可路由的 IP 提供，因此不会在 VM 外部公开。

首次为事件发出请求时，会为规模集启用 Scheduled Events。 首次调用时可能会延迟响应最多两分钟。 定期查询终结点，以便检测即将发生的维护事件以及正在进行的维护活动的状态。

如果规模集实例在 24 小时内未发出请求，则为规模集禁用 Scheduled Events。

### <a name="endpoint-discovery"></a>终结点发现
对于启用了 VNET 的 VM，元数据服务可通过不可路由的静态 IP (169.254.169.254) 使用。

最新版本的计划事件的完整终结点是：
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="query-response"></a>查询响应
响应包含计划事件的数组。 数组为空意味着目前没有计划事件。

如果有计划事件，响应会包含事件的数组。 对于“终止”事件，响应将如下所示：
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
DocumentIncarnation 是一个 ETag，它提供了一种简单的方法来检查自上次查询以来事件有效负载是否已更改。

若要详细了解上述每个字段，请参阅适用于 [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) 和 [Linux](../virtual-machines/linux/scheduled-events.md#event-properties) 的 Scheduled Events 文档。

### <a name="respond-to-events"></a>响应事件
了解即将发生的事件并完成正常关闭逻辑后，可以通过使用 EventId 对元数据服务进行 POST 调用来批准未完成的事件。 POST 调用向 Azure 指示可以继续删除 VM。

下面是 POST 请求正文中所需的 json。 请求应包含 StartRequests 列表。 每个 StartRequest 都包含要加速的事件的 EventId：
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

确保规模集中的每个 VM 仅批准与该 VM 相关的 EventID。 VM 可以[通过实例元数据](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)获取自身的 VM 名称。 此名称采用“{scale-set-name}_{instance-id}”的格式，将显示在上述查询响应的“资源”部分中。

还可以参阅示例脚本，来查询和响应事件 [Python ](../virtual-machines/linux/scheduled-events.md#python-sample)。

## <a name="tips-and-best-practices"></a>提示和最佳实践
-   仅在 "删除" 操作上终止通知–所有删除操作 (手动删除或自动缩放启动的) 如果规模集已启用 *scheduledEventsProfile* ，则将生成终止事件。 重新启动、重置映像、重新部署和停止/解除分配等其他操作不会生成终止事件。 无法为低优先级 VM 启用终止通知。
-   无强制等待超时–您可以在收到事件之后、事件的 *NotBefore* 时间到期之前随时开始终止操作。
-   在超时时必需删除–生成事件后，不能扩展超时值。 超时过期后，将处理挂起的终止事件，并删除 VM。
-   可修改超时值–可以在删除实例之前随时修改超时值，方法是修改规模集模型中的 *notBeforeTimeout* 属性，并将 VM 实例更新到最新模型。
-   批准所有挂起的删除-如果 VM_1 上有一个挂起的删除未批准，并且你已在 VM_2 上批准了另一个终止事件，则在 VM_1 的 "终止" 事件被批准或已过超时之前，不会删除 VM_2。 批准 VM_1 终止事件后，将删除 VM_1 和 VM_2。
-   批准所有同时删除–扩展上面的示例，如果 VM_1 和 VM_2 具有相同的 *NotBefore* 时间，则必须批准两个终止事件，否则在超时过期之前，都不会删除 VM。

## <a name="troubleshoot"></a>故障排除
### <a name="failure-to-enable-scheduledeventsprofile"></a>无法启用 scheduledEventsProfile
如果收到“错误的请求”错误并显示错误消息“在‘VirtualMachineProfile’类型的对象中找不到成员‘scheduledEventsProfile’”，请检查用于规模集操作的 API 版本。 需要计算 API 版本 2019-03-01 或更高版本。 

### <a name="failure-to-get-terminate-events"></a>未能获取终止事件
如果无法通过 Scheduled Events 获取任何终止事件，请检查用于获取事件的 API 版本。 终止事件需要元数据服务 API 版本 2019-01-01 或更高版本。
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>通过不正确的 NotBefore 时间获取终止事件  
对规模集模型启用 scheduledEventsProfile 并设置 notBeforeTimeout 后，将各个实例更新为[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改 。

## <a name="next-steps"></a>后续步骤
了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
