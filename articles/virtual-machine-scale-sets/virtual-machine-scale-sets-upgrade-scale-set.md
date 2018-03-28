---
title: 修改 Azure 虚拟机规模集 | Microsoft Docs
description: 修改 Azure 虚拟机规模集
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>修改虚拟机规模集
本文介绍如何修改现有的虚拟机规模集。 任务包括如何更改规模集的配置、如何更改在规模集上运行的应用程序的配置、如何管理可用性，等等。

## <a name="fundamental-concepts"></a>基本概念

### <a name="scale-set-model"></a>规模集模型

规模集有一个模型，用于以整体方式捕获规模集的所需状态。 若要查询规模集的模型，可使用以下命令：

* REST API： 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get)。

* PowerShell：

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  有关详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)。

* Azure CLI： 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  有关详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show)。

也可使用 [Azure 资源浏览器（预览版）](https://resources.azure.com)或 [Azure SDK](https://azure.microsoft.com/downloads/) 来查询规模集的模型。

输出的具体呈现取决于提供给命令的选项。 下面是 Azure CLI 的示例输出：

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
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
  .
  .
  .
}
```

可以看到，这些属性作为一个整体应用到规模集。



### <a name="scale-set-instance-view"></a>规模集实例视图

规模集还有一个实例视图，用于以整体方式捕获规模集当前的运行时状态。 若要查询规模集的实例视图，可使用以下命令：

* REST API： 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview)。

* PowerShell： 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  有关详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)。

* Azure CLI： 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  有关详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view)。

也可使用 [Azure 资源浏览器（预览版）](https://resources.azure.com)或 [Azure SDK](https://azure.microsoft.com/downloads/) 来查询规模集的实例视图。

输出的具体呈现取决于提供给命令的选项。 下面是 Azure CLI 的示例输出：

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
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
  .
  .
  .
}
```

可以看到，这些属性汇总了规模集中 VM 的当前运行时状态。 摘要中包括应用到规模集的扩展的状态（为简便起见，已省略）。



### <a name="scale-set-vm-model-view"></a>规模集 VM 模型视图

规模集中的每个 VM 都有自己的模型视图，这类似于每个规模集都有模型视图的情况。 若要查询规模集的模型视图，可使用以下命令：

* REST API： 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get)。

* PowerShell： 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  有关详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)。

* Azure CLI： 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  有关详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show)。

也可使用 [Azure 资源浏览器（预览版）](https://resources.azure.com)或 [Azure SDK](https://azure.microsoft.com/downloads/) 来查询规模集中 VM 的模型。

输出的具体呈现取决于提供给命令的选项。 下面是 Azure CLI 的示例输出：

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

可以看到，这些属性描述的是 VM 本身的配置，而不是规模集作为一个整体的配置。 例如，规模集模型使用 `overprovision` 作为属性，而规模集中 VM 的模型则不是这样。 之所以存在这种差异，是因为过度预配是规模集作为一个整体的属性，而不是规模集中各个 VM 的属性。 （有关过度预配的详细信息，请参阅[规模集的设计注意事项](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)。）



### <a name="scale-set-vm-instance-view"></a>规模集 VM 实例视图

规模集中的每个 VM 都有自己的实例视图，这类似于每个规模集都有实例视图的情况。 若要查询规模集的实例视图，可使用以下命令：

* REST API： 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview)。

* PowerShell： 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  有关详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)。

* Azure CLI： 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  有关详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view)。

也可使用 [Azure 资源浏览器（预览版）](https://resources.azure.com)或 [Azure SDK](https://azure.microsoft.com/downloads/) 来查询规模集中 VM 的实例视图。

输出的具体呈现取决于提供给命令的选项。 下面是 Azure CLI 的示例输出：

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
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
  .
  .
  .
}
```

可以看到，这些属性描述的是 VM 本身的当前运行时状态。 状态包括应用到规模集的任何扩展（为简便起见，已省略）。




## <a name="techniques-for-updating-global-scale-set-properties"></a>更新全局规模集属性的方法

若要更新某个全局规模集属性，必须在规模集模型中更新该属性。 可通过以下方式进行该更新：

* REST API： 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)。

  可以使用 REST API 中的属性部署 Azure 资源管理器模板，以便更新全局规模集属性。

* PowerShell： 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  有关详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss)。

* Azure CLI：

  * 修改属性：`az vmss update --set {propertyPath}={value}`。 
  
  * 向规模集中的列表属性添加对象：`az vmss update --add {propertyPath} {JSONObjectToAdd}`。 
  
  * 从规模集中的列表属性删除对象：`az vmss update --remove {propertyPath} {indexToRemove}`。 
  
  有关详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)。 
  
  或者，如果此前已使用 `az vmss create` 命令部署规模集，则可再次运行 `az vmss create` 命令来更新规模集。 为此，请确保 `az vmss create` 命令中的所有属性都与以前的一样，要修改的属性除外。



也可使用 [Azure 资源浏览器（预览版）](https://resources.azure.com)或 [Azure SDK](https://azure.microsoft.com/downloads/) 来更新规模集模型。

更新规模集模型以后，新配置就会应用到在规模集中创建的新 VM。 但是，规模集中现有 VM 的模型仍需使用总体说来最新的规模集模型进行更新。 在每个 VM 的模型中有一个名为 `latestModelApplied` 的布尔属性，该属性指示 VM 是否已使用总体来说最新的规模集模型进行更新。 （`true` 值表示已使用最新模型更新 VM。）




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>使用最新规模集模型对 VM 进行更新的方法

规模集有一项升级策略，该策略决定了如何使用最新的规模集模型对 VM 进行更新。 升级策略的三种模式是：

- **自动**：在此模式下，规模集无法保证 VM 的关闭顺序。 规模集可能同时关闭所有 VM。 
- **滚动**：在此模式下，规模集以批次的方式推出更新，批次之间的暂停时间为可选。
- **手动**：在此模式下，对规模集模型进行更新时，现有的 VM 不会发生任何事情。 若要更新现有的 VM，必须手动升级每个 VM。 可通过以下方式进行此手动升级：

  - REST API： 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances)。

  - PowerShell： 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    有关详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance)。

  - Azure CLI： 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    有关详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances)。

  也可使用 [Azure SDK](https://azure.microsoft.com/downloads/) 手动升级规模集中的 VM。

>[!NOTE]
> Azure Service Fabric 群集只能使用“自动”模式，但采用不同方式来处理更新。 有关 Service Fabric 更新的详细信息，请参阅 [Service Fabric 文档](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade)。

有一种对全局规模集属性的修改不遵循升级策略：对规模集 OS 配置文件的更改。 （例如，管理员用户名和密码。）只能在 API 版本 2017-12-01 或更高版本中更改这些属性。 这些更改仅适用于在对规模集模型进行更改后创建的 VM。 若要更新现有的 VM，必须对每个现有的 VM 执行重置映像。 可通过以下方式对 VM 执行重置映像：

* REST API： 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)。

* PowerShell： 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  有关详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm)。

* Azure CLI： 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  有关详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)。

也可使用 [Azure SDK](https://azure.microsoft.com/downloads/) 对规模集中的 VM 重置映像。




## <a name="properties-with-restrictions-on-modification"></a>对修改有限制的属性

### <a name="create-time-properties"></a>创建时属性

某些属性只能在最初创建规模集时设置。 这些属性包括：

- 区域
- Image reference publisher
- image reference offer

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>只能在当前值的基础上更改的属性

某些属性可以更改，但也有例外，具体取决于当前值。 这些属性包括：

- `singlePlacementGroup`：如果 `singlePlacementGroup` 为 true，则可将其修改为 false。 但是，如果 `singlePlacementGroup` 为 false，则不可将其修改为 true。
- `subnet`：修改规模集的子网的前提是，原始子网和新子网在同一虚拟网络中。

### <a name="properties-that-require-deallocation-to-change"></a>需要解除分配才能更改的属性

某些属性只有在规模集中的 VM 已解除分配的情况下，才能更改为特定值。 这些属性包括：

- `sku name`：如果新 VM SKU 在规模集当前所在的硬件上不受支持，则需先将规模集中的 VM 解除分配，然后才能修改 `sku name`。 若要详细了解如何重设 VM 大小，请参阅[此 Azure 博客文章](https://azure.microsoft.com/blog/resize-virtual-machines/)。


## <a name="vm-specific-updates"></a>特定于 VM 的更新

某些修改可以应用于特定的 VM，但不能应用于全局规模集属性。 目前，受支持的唯一特定于 VM 的更新是将数据磁盘附加到规模集中的 VM 或者从其分离数据磁盘。 此功能为预览版。 有关详细信息，请参阅[预览版文档](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)。

## <a name="scenarios"></a>方案

### <a name="application-updates"></a>应用程序更新

如果应用程序已通过扩展部署到规模集，则更新扩展配置会导致应用程序按升级策略进行更新。 例如，如果新版脚本在自定义脚本扩展中运行，则可更新 `fileUris` 属性，使之指向新脚本。 

但在某些情况下，可能需要强制进行更新，即使扩展配置未更改。 （例如，在未更改脚本 URI 的情况下更新了脚本。）在这些情况下，可以通过修改 `forceUpdateTag` 来强制进行更新。 Azure 平台不解释此属性，因此更改其值不影响扩展的具体运行。 修改它只是强制扩展重新运行。 

有关 `forceUpdateTag` 的详细信息，请参阅[针对扩展的 REST API 文档](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate)。

通过自定义映像来部署应用程序也很常见。 以下部分介绍此情景。

### <a name="os-updates"></a>OS 更新

如果使用平台映像，可以通过修改 `imageReference` 来更新映像。 有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)。

>[!NOTE]
> 使用平台映像时，通常指定 "latest" 作为映像引用版本。 这意味着，在完成规模集的创建、横向扩展和重置映像操作时，VM 使用最新发布的版本创建。 但是，这*并不*意味着 OS 映像会随新映像版本的发布自动进行更新。 这是独立的功能，目前为预览版。 有关详细信息，请参阅[自动 OS 升级](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)。

如果使用自定义映像，可以通过更新 `imageReference` ID 来更新映像。 有关详细信息，请参阅 [REST API 文档](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)。

## <a name="examples"></a>示例

### <a name="update-the-os-image-for-your-scale-set"></a>更新规模集的 OS 映像

假设规模集运行旧版 Ubuntu LTS 16.04， 你需要将其更新到新版 Ubuntu LTS 16.04（例如，版本 16.04.201801090）。 映像引用版本属性不是列表的一部分，因此可以使用以下命令直接修改这些属性：

* PowerShell： 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* Azure CLI： 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>更新规模集的负载均衡器

假设在规模集带有 Azure 负载均衡器的情况下，需将负载均衡器替换为 Azure 应用程序网关。 规模集的负载均衡器和应用程序网关属性是列表的一部分。 因此，可以使用命令来删除和添加列表元素，而不必直接修改属性。

PowerShell：
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Azure CLI：
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> 这些命令假定规模集上只有一个 IP 配置和负载均衡器。 如果有多个，则可能需要使用 0 之外的列表索引。