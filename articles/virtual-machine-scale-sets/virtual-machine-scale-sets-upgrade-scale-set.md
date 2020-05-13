---
title: 修改 Azure 虚拟机规模集
description: 了解如何使用 REST API、Azure PowerShell 和 Azure CLI 修改和更新 Azure 虚拟机规模集
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 9498babd9605c46d752c5fe1eb1b077f6d911351
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121008"
---
# <a name="modify-a-virtual-machine-scale-set"></a>修改虚拟机规模集

在应用程序的整个生命周期内，你可能需要修改或更新你的虚拟机规模集。 这些更新可能包括更新规模集的配置，或更改应用程序配置。 本文介绍了如何使用 REST API、Azure PowerShell 或 Azure CLI 修改现有规模集。

## <a name="fundamental-concepts"></a>基本概念

### <a name="the-scale-set-model"></a>规模集模型
规模集有一个“规模集模型”，用于以整体方式捕获规模集的所需状态。  若要查询规模集的模型，可使用以下命令： 

- 如下所示通过 REST API 使用 [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get)：

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- 通过 Azure PowerShell 使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)：

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- 通过 Azure CLI 使用 [az vmss show](/cli/azure/vmss)：

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- 还可以使用 [resources.azure.com](https://resources.azure.com) 或特定于语言的 [Azure SDK](https://azure.microsoft.com/downloads/)。

输出的具体呈现取决于提供给命令的选项。 下面的示例显示了来自 Azure CLI 的精简版示例输出：

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

这些属性作为一个整体应用到规模集。


### <a name="the-scale-set-instance-view"></a>规模集实例视图
规模集还有一个“规模集实例视图”，用于以整体方式捕获规模集当前的“运行时”状态。  若要查询规模集的实例视图，可使用以下命令：

- 如下所示通过 REST API 使用 [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview)：

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- 通过 Azure PowerShell 使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)：

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- 通过 Azure CLI 使用 [az vmss get-instance-view](/cli/azure/vmss)：

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- 还可以使用[resources.azure.com](https://resources.azure.com)或特定于语言的[azure sdk](https://azure.microsoft.com/downloads/)

输出的具体呈现取决于提供给命令的选项。 下面的示例显示了来自 Azure CLI 的精简版示例输出：

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

这些属性汇总了规模集中 VM 的当前运行时状态，例如应用于规模集的扩展的状态。


### <a name="the-scale-set-vm-model-view"></a>规模集 VM 模型视图
规模集中的每个 VM 实例都有自己的模型视图，这类似于每个规模集都有模型视图的情况。 若要查询规模集中特定 VM 实例的模型视图，可使用以下命令：

- 如下所示通过 REST API 使用 [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get)：

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- 通过 Azure PowerShell 使用 [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm)：

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- 通过 Azure CLI 使用 [az vmss show](/cli/azure/vmss)：

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- 还可以使用 [resources.azure.com](https://resources.azure.com) 或 [Azure SDK](https://azure.microsoft.com/downloads/)。

输出的具体呈现取决于提供给命令的选项。 下面的示例显示了来自 Azure CLI 的精简版示例输出：

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

这些属性描述的是规模集中某个 VM 实例的配置，而不是规模集作为一个整体的配置。 例如，规模集模型使用 `overprovision` 作为属性，而规模集中 VM 实例的模型则不是这样。 之所以存在这种差异，是因为过度预配是规模集作为一个整体的属性，而不是规模集中各个 VM 实例的属性（有关过度预配的详细信息，请参阅[规模集的设计注意事项](virtual-machine-scale-sets-design-overview.md#overprovisioning)）。


### <a name="the-scale-set-vm-instance-view"></a>规模集 VM 实例视图
规模集中的每个 VM 实例都有自己的实例视图，这类似于每个规模集都有实例视图的情况。 若要查询规模集中特定 VM 实例的实例视图，可使用以下命令：

- 通过 REST API 使用 [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)：

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- 通过 Azure PowerShell 使用 [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm)：

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- 通过 Azure CLI 使用 [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- 还可以使用[resources.azure.com](https://resources.azure.com)或[azure sdk](https://azure.microsoft.com/downloads/)

输出的具体呈现取决于提供给命令的选项。 下面的示例显示了来自 Azure CLI 的精简版示例输出：

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

这些属性描述的是规模集中 VM 实例的当前运行时状态，包括应用于规模集的任何扩展。


## <a name="how-to-update-global-scale-set-properties"></a>如何更新全局规模集属性
若要更新某个全局规模集属性，必须在规模集模型中更新该属性。 可通过以下方式进行该更新：

- 如下所示通过 REST API 使用 [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate)：

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- 可以使用 REST API 中的属性部署资源管理器模板，以便更新全局规模集属性。

- 通过 Azure PowerShell 使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)：

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- 通过 Azure CLI 使用 [az vmss update](/cli/azure/vmss)：
    - 修改属性：

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - 向规模集中的列表属性添加对象： 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - 从规模集中的列表属性删除对象： 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - 如果此前已使用 `az vmss create` 命令部署了规模集，则可再次运行 `az vmss create` 命令来更新规模集。 请确保 `az vmss create` 命令中的所有属性都与以前的一样，要修改的属性除外。

- 还可以使用 [resources.azure.com](https://resources.azure.com) 或 [Azure SDK](https://azure.microsoft.com/downloads/)。

更新规模集模型以后，新配置就会应用到在规模集中创建的新 VM。 但是，规模集中现有 VM 的模型仍需使用总体说来最新的规模集模型进行更新。 在每个 VM 的模型中有一个名为 `latestModelApplied` 的布尔属性，该属性指示 VM 是否已使用总体来说最新的规模集模型进行更新（`true` 意味着 VM 已使用最新模型进行更新）。


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>如何使用最新的规模集模型对 VM 进行更新
规模集有一项“升级策略”，该策略决定了如何使用最新的规模集模型对 VM 进行更新。 升级策略的三种模式是：

- **自动** - 在此模式下，规模集无法保证 VM 的关闭顺序。 规模集可能同时关闭所有 VM。 
- **滚动** - 在此模式下，规模集以批次的方式推出更新，批次之间的暂停时间为可选。
- **手动** - 在此模式下，对规模集模型进行更新时，现有的 VM 不会发生任何事情。
 
若要更新现有的 VM，必须对每个现有的 VM 进行“手动升级”。 可通过以下方式进行此手动升级：

- 如下所示通过 REST API 使用 [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances)：

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- 通过 Azure PowerShell 使用 [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance)：
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- 通过 Azure CLI 使用 [az vmss update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- 还可以使用特定于语言的 [Azure SDK](https://azure.microsoft.com/downloads/)。

>[!NOTE]
> Service Fabric 群集只能使用“自动”模式，但采用不同方式来处理更新。  有关详细信息，请参阅 [Service Fabric 应用程序升级](../service-fabric/service-fabric-application-upgrade.md)。

有一类对全局规模集属性的修改不遵循升级策略。 只能通过 API 版本 *2017-12-01* 或更高版本更改规模集 OS 和数据磁盘配置文件（例如管理员用户名和密码）。 这些更改仅适用于在对规模集模型进行更改后创建的 VM。 若要更新现有的 VM，必须对每个现有的 VM 执行“重置映像”操作。 可通过以下方式执行此重置映像操作：

- 如下所示通过 REST API 使用 [compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage)：

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- 通过 Azure PowerShell 使用 [Set-AzVmssVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)：

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- 通过 Azure CLI 使用 [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss)：

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- 还可以使用特定于语言的 [Azure SDK](https://azure.microsoft.com/downloads/)。


## <a name="properties-with-restrictions-on-modification"></a>对修改有限制的属性

### <a name="create-time-properties"></a>创建时属性
某些属性只能在创建规模集时设置。 这些属性包括：

- 可用性区域
- Image reference publisher
- image reference offer
- 托管 OS 磁盘存储帐户类型

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>只能在当前值的基础上更改的属性
某些属性可以更改，但也有例外，具体取决于当前值。 这些属性包括：

- **singlePlacementGroup** - 如果 singlePlacementGroup 为 true，则可将其修改为 false。 但是，如果 singlePlacementGroup 为 false，则**不可**将其修改为 true。
- **subnet** - 修改规模集的子网的前提是，原始子网和新子网在同一虚拟网络中。

### <a name="properties-that-require-deallocation-to-change"></a>需要解除分配才能更改的属性
某些属性只有在规模集中的 VM 已解除分配的情况下，才能更改为特定值。 这些属性包括：

- **SKU 名称** - 如果新 VM SKU 在规模集当前所在的硬件上不受支持，则需先将规模集中的 VM 解除分配，然后才能修改 SKU 名称。 有关详细信息，请参阅[如何调整 Azure VM 的大小](../virtual-machines/windows/resize-vm.md)。


## <a name="vm-specific-updates"></a>特定于 VM 的更新
某些修改可以应用于特定的 VM，但不能应用于全局规模集属性。 目前，唯一受支持的特定于 VM 的更新是将数据磁盘附加到规模集中的 VM 或者从其分离数据磁盘。 此功能为预览版。 有关详细信息，请参阅[预览版文档](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)。


## <a name="scenarios"></a>方案

### <a name="application-updates"></a>应用程序更新
如果应用程序已通过扩展部署到规模集，则更新扩展配置会导致应用程序按升级策略进行更新。 例如，如果新版脚本在自定义脚本扩展中运行，则可更新 *fileUris* 属性，使之指向新脚本。 在某些情况下，可能需要强制进行更新，即使扩展配置未更改（例如，在未更改脚本 URI 的情况下更新脚本）。 在这些情况下，可以通过修改 *forceUpdateTag* 来强制进行更新。 Azure 平台不解释此属性。 如果更改此值，不会影响扩展的运行方式。 更改只是会强制扩展重新运行。 有关 *forceUpdateTag* 的详细信息，请参阅[针对扩展的 REST API 文档](/rest/api/compute/virtualmachineextensions/createorupdate)。 请注意，*forceUpdateTag* 可用于所有扩展，而不仅仅是自定义脚本扩展。

通过自定义映像来部署应用程序也很常见。 以下部分介绍此情景。

### <a name="os-updates"></a>操作系统更新
如果使用 Azure 平台映像，可以通过修改 *imageReference* 来更新映像（有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)）。

>[!NOTE]
> 使用平台映像时，通常指定 "latest" 作为映像引用版本。 在你执行创建、横向扩展和重置映像操作时，将使用最新发布的脚本创建 VM。 但是，这**并不**意味着 OS 映像会随新映像版本的发布自动进行更新。 当前处于预览版状态的一个独立功能提供了自动 OS 升级功能。 有关详细信息，请参阅[自动 OS 升级文档](virtual-machine-scale-sets-automatic-upgrade.md)。

如果使用自定义映像，可以通过更新 *imageReference* ID 来更新映像（有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)）。

## <a name="examples"></a>示例

### <a name="update-the-os-image-for-your-scale-set"></a>更新规模集的 OS 映像
你可能具有运行旧版 Ubuntu LTS 16.04 的规模集。 你希望将其更新到新版 Ubuntu LTS 16.04，例如版本 *16.04.201801090*。 映像引用版本属性不是列表的一部分，因此可以使用下列命令之一直接修改这些属性：

- 如下所示，通过 Azure PowerShell 使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)：

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- 通过 Azure CLI 使用 [az vmss update](/cli/azure/vmss)：

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

或者，你可能想要更改规模集使用的映像。 例如，你可能想要更新或更改规模集使用的自定义映像。 可以通过更新“映像引用 ID”属性来更改规模集使用的映像。 “映像引用 ID”属性不是列表的一部分，因此可以使用下列命令之一直接修改该属性：

- 如下所示，通过 Azure PowerShell 使用 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)：

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- 通过 Azure CLI 使用 [az vmss update](/cli/azure/vmss)：

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>更新规模集的负载均衡器
假设在规模集带有 Azure 负载均衡器的情况下，需要将 Azure 负载均衡器替换为 Azure 应用程序网关。 规模集的负载均衡器和应用程序网关属性是列表的一部分，因此可以使用以下命令来删除或添加列表元素，而不必直接修改属性：

- Azure Powershell：

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI：

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> 这些命令假定规模集上只有一个 IP 配置和负载均衡器。 如果有多个，则可能需要使用 *0* 之外的列表索引。


## <a name="next-steps"></a>后续步骤
还可以使用 [Azure CLI](virtual-machine-scale-sets-manage-cli.md) 或 [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md) 对规模集执行常见的管理任务。
