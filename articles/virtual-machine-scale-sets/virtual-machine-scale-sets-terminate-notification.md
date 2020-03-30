---
title: 终止 Azure 虚拟机缩放集实例的通知
description: 了解如何为 Azure 虚拟机缩放集实例启用终止通知
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250746"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>终止 Azure 虚拟机缩放集实例的通知
缩放集实例可以选择接收实例终止通知，并将预定义的延迟超时设置为终止操作。 终止通知通过 Azure 元数据服务 （[计划事件](../virtual-machines/windows/scheduled-events.md)） 发送，该服务为影响性操作（如重新启动和重新部署）提供通知和延迟。 解决方案将另一个事件 （终止 ） 添加到计划事件列表中，终止事件的关联延迟将取决于用户在其规模集模型配置中指定的延迟限制。

注册到该功能后，缩放集实例无需等待指定的超时过期，然后删除实例。 收到终止通知后，实例可以选择在终止超时到期之前随时删除。

## <a name="enable-terminate-notifications"></a>启用终止通知
在规模设置实例上启用终止通知的方法有多种，如下例中详细说明。

### <a name="azure-portal"></a>Azure 门户

在创建新比例集时，以下步骤启用终止通知。 

1. 转到**虚拟机缩放集**。
1. 选择 **+ 添加**以创建新的比例集。
1. 转到 **"管理"** 选项卡。 
1. 查找**实例终止**部分。
1. 对于**实例终止通知**，请选择 **"打开**"。
1. 对于**终止延迟（分钟），** 设置所需的默认超时。
1. 创建新比例集后，选择 **"查看 + 创建**"按钮。 

> [!NOTE]
> 无法在 Azure 门户中的现有规模集上设置终止通知

### <a name="rest-api"></a>REST API

以下示例在规模集模型上启用终止通知。

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

上述块为缩放集中的所有实例的任何终止操作指定 5 分钟的超时延迟（如*PT5M*所示）。 在 ISO 8601 格式中 *，不执行之前的*字段可以获取 5 到 15 分钟之间的任何值。 您可以通过修改上述 *"终止通知配置文件*"下的 *"不前超时*"属性来更改终止操作的默认超时。

在比例集模型上启用*计划事件配置文件*并设置 *"不前超时*"后，将各个实例更新到[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改。

> [!NOTE]
>只能使用 API 版本 2019-03-01 及以上启用规模设置实例上的终止通知

### <a name="azure-powershell"></a>Azure PowerShell
创建新比例集时，可以使用[New-AzVmsConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 在比例集中启用终止通知。

此示例脚本使用配置文件浏览比例集和相关资源的创建：[创建完整的虚拟机规模集](./scripts/powershell-sample-create-complete-scale-set.md)。 您可以通过将*参数终止计划事件*和*终止计划事件 Not 在TimeoutIn分钟之前*添加到配置对象以创建比例集来提供配置终止通知。 以下示例启用延迟超时为 10 分钟的功能。

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

使用[Update-AzVms](/powershell/module/az.compute/update-azvmss) cmdlet 在现有规模集中启用终止通知。

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
上述示例启用现有规模集中的终止通知，并为终止事件设置 15 分钟的超时。

在比例集模型上启用计划事件并设置超时后，将各个实例更新到[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改。

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下示例用于在创建新比例集时启用终止通知。

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

上面的示例首先创建一个资源组，然后创建一个新的比例集，并启用了 10 分钟的默认超时终止通知。

以下示例用于在现有规模集中启用终止通知。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>获取终止通知

终止通知通过[计划事件](../virtual-machines/windows/scheduled-events.md)传递 ，这是 Azure 元数据服务。 Azure 元数据服务使用可从 VM 内访问的 REST 终结点公开有关正在运行的虚拟机的信息。 信息可通过不可路由的 IP 获得，因此不会在 VM 外部公开。

首次请求事件时，会为规模设置启用计划事件。 您可以在第一次呼叫中延迟响应长达两分钟。 定期查询终结点，以检测即将发生的维护事件和正在进行的维护活动的状态。

如果比例集实例在 24 小时内未发出请求，则为规模集禁用计划事件。

### <a name="endpoint-discovery"></a>端点发现
对于启用 VNET 的 VM，元数据服务可从静态不可路由 IP 169.254.169.254 获得。

最新版本的计划事件的完整终结点是：
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>查询响应
响应包含计划事件的数组。 空数组表示目前没有计划事件。

在有计划事件的情况下，响应包含事件数组。 对于"终止"事件，响应如下所示：
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
*DocumentIncarnation*是一个 ETag，它提供了一种简单的方法来检查事件负载自上次查询以来是否已更改。

有关上述每个字段的详细信息，请参阅[Windows](../virtual-machines/windows/scheduled-events.md#event-properties)和[Linux](../virtual-machines/linux/scheduled-events.md#event-properties)的"计划事件"文档。

### <a name="respond-to-events"></a>响应事件
一旦您得知即将发生的事件并完成了正常关闭的逻辑，您可以通过使用 EventId 对元数据服务进行 POST 调用来批准未完成的事件。 POST 调用向 Azure 指示可以继续删除 VM。

下面是 POST 请求正文中预期中的 json。 请求应包含启动请求的列表。 每个启动请求都包含要加速的事件的事件事件 Id：
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

确保规模集中的每个 VM 仅批准与该 VM 相关的 EventID。 VM 可以通过[实例元数据](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)获取自己的 VM 名称。 此名称采用"[缩放集名称][实例-id]"的形式，将显示在上述查询响应的"资源"部分中。

您还可以使用[PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample)和[Python](../virtual-machines/linux/scheduled-events.md#python-sample)引用示例脚本来查询和响应事件。

## <a name="tips-and-best-practices"></a>提示和最佳实践
-   仅在"删除"操作上终止通知 - 如果规模集已启用*计划事件配置文件*，则所有删除操作（手动删除或自动缩放启动的缩放）将生成终止事件。 重新启动、重新映像、重新部署和停止/取消分配等其他操作不会生成终止事件。 无法为低优先级 VM 启用终止通知。
-   无需强制等待超时 - 您可以在收到事件后和事件"*不提前之前"* 之前随时启动终止操作。
-   超时时强制删除 – 生成事件后无法扩展超时值。 超时过期后，将处理挂起的终止事件，并删除 VM。
-   可修改超时值 – 您可以通过修改规模集模型*上的 notAttimeout*属性并将 VM 实例更新为最新模型，在删除实例之前的任何时间修改超时值。
-   批准所有挂起的删除 - 如果未批准的VM_1上存在挂起的删除，并且您已批准VM_2上的另一个终止事件，则VM_2在批准VM_1终止事件或其超时已过之前不会删除。 批准VM_1终止事件后，将删除VM_1和VM_2。
-   批准所有同时删除 – 扩展上述示例，如果VM_1和VM_2在*时间之前具有相同的，* 则必须批准两个终止事件，或者在超时到期之前两个 VM 都不会删除。

## <a name="troubleshoot"></a>疑难解答
### <a name="failure-to-enable-scheduledeventsprofile"></a>无法启用计划事件配置文件
如果收到"BadRequest"错误，并出现错误消息，指出"无法在类型为"虚拟机配置文件"的对象上找到成员'计划事件配置文件'"，请检查用于缩放集操作的 API 版本。 需要计算 API 版本**2019-03-01**或更高版本。 

### <a name="failure-to-get-terminate-events"></a>无法获取终止事件
如果无法通过计划事件获取任何**终止**事件，请检查用于获取事件的 API 版本。 终止事件需要元数据服务 API 版本**2019-01-01**或更高版本。
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>获取与不正确的"未之前"事件  
在比例集模型上启用*计划事件配置文件*并设置 *"不前超时*"后，将各个实例更新到[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改。

## <a name="next-steps"></a>后续步骤
了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
