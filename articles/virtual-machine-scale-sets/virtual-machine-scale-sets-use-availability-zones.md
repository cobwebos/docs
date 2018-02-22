---
title: "创建使用可用性区域（预览版）的 Azure 规模集 | Microsoft Docs"
description: "了解如何创建使用可用性区域的 Azure 虚拟机规模集以增加冗余防止中断"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 2de214f604469025a8a4accde44359fea0ded7e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>创建使用可用性区域（预览版）的虚拟机规模集
若要防止虚拟机规模集发生数据中心级故障，可以跨可用性区域创建规模集。 支持可用性区域的 Azure 区域最少具有三个单独的区域，每个都有其自己的独立电源、网络和冷却设备。 有关详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>单区域和区域冗余规模集
部署虚拟机规模集时，可选择在区域中使用单个可用性区域或多个区域。

在单个区域中创建规模集时，可控制要运行所有这些 VM 实例的区域，规模集仅在该区域中进行托管和自动缩放。 使用区域冗余规模集，可创建跨越多个区域的单个规模集。 创建 VM 实例时，默认其在各区域中同等均衡。 如果其中一个区域中发生中断，规模集不会自动扩大以增加容量。 最佳做法是基于 CPU 或内存使用情况配置自动缩放规则。 借助自动缩放规则，规模集能够通过在其余可操作区域中横向扩展新实例，来应对该区域中的 VM 实例损失。

若要使用可用性区域，必须在[受支持的 Azure 区域](../availability-zones/az-overview.md#regions-that-support-availability-zones)中创建规模集。 还需要[注册可用性区域预览版](http://aka.ms/azenroll)。 可以使用下列方法之一创建使用可用性区域的规模集：

- [Azure portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure 资源管理器模板](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>使用 Azure 门户
创建使用可用性区域的规模集的过程与[入门文章](virtual-machine-scale-sets-create-portal.md)中详述的过程相同。 请确保已[注册可用性区域预览版](http://aka.ms/azenroll)。 选择一个受支持的 Azure 区域后，可以在可用性区域之一中创建规模集，如以下示例中所示：

![在单个可用性区域中创建规模集](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

将在你指定的单个区域中创建规模集和支持资源，例如 Azure 负载均衡器和公共 IP 地址。


## <a name="use-the-azure-cli-20"></a>使用 Azure CLI 2.0
创建使用可用性区域的规模集的过程与[入门文章](virtual-machine-scale-sets-create-cli.md)中详述的过程相同。 为使用可用性区域，必须在受支持的 Azure 区域中创建程序集，并[注册可用性区域预览版](http://aka.ms/azenroll)。

在 [az vmss create](/cli/azure/vmss#az_vmss_create) 命令中添加 `--zones` 参数并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。 以下示例在区域 1 中创建名为 myScaleSet 的单个区域规模集：

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```
有关单区域规模集和网络资源的完整示例，请参阅[此示例 CLI 脚本](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>区域冗余规模集
若要创建区域冗余规模集，请使用标准 SKU 公用 IP 地址和负载均衡器。 为增强冗余，标准 SKU 创建区域冗余网络资源。 有关详细信息，请参阅 [Azure 负载均衡器标准概述](../load-balancer/load-balancer-standard-overview.md)。 第一次创建区域冗余规模集或负载均衡器时，必须完成以下步骤为帐户注册这些预览版功能。

1. 使用 [az feature register](/cli/azure/feature#az_feature_register) 为帐户注册区域冗余规模集和网络功能，如下所示：

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. 可能需要几分钟来注册这些功能。 可以使用 [az feature show](/cli/azure/feature#az_feature_show) 检查操作的状态：

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    下例显示所需的功能状态为“已注册”：
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. 当区域冗余规模集和网络资源报告为“已注册”后，立即使用 [az provider register](/cli/azure/provider#az_provider_register) 重新注册“计算”和“网络”提供程序，如下所示：

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

若要创建区域冗余规模集，可使用 `--zones` 参数指定多个区域。 下例跨区域 1、2、3 创建名为 myScaleSet 的区域冗余规模集：

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

在指定的区域中创建和配置所有规模集资源和 VM 需要几分钟时间。 有关区域冗余规模集和网络资源的完整示例，请参阅[此示例 CLI 脚本](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
创建使用可用性区域的规模集的过程与[入门文章](virtual-machine-scale-sets-create-powershell.md)中详述的过程相同。 为使用可用性区域，必须在受支持的 Azure 区域中创建程序集，并[注册可用性区域预览版](http://aka.ms/azenroll)。 在 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) 命令中添加 `-Zone` 参数并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。 

以下示例在美国东部 2 区域 1 中创建名为 vmssConfig 的单个区域规模集配置：

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

有关单个区域规模集和网络资源的完整示例，请参阅[此示例 PowerShell 脚本](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)

### <a name="zone-redundant-scale-set"></a>区域冗余规模集
若要创建区域冗余规模集，请使用标准 SKU 公用 IP 地址和负载均衡器。 为增强冗余，标准 SKU 创建区域冗余网络资源。 有关详细信息，请参阅 [Azure 负载均衡器标准概述](../load-balancer/load-balancer-standard-overview.md)。 第一次创建区域冗余规模集或负载均衡器时，必须完成以下步骤为帐户注册这些预览版功能。

1. 使用 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 为帐户注册区域冗余规模集和网络功能，如下所示：

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. 可能需要几分钟来注册这些功能。 可以使用 [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 检查操作的状态：

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    下例显示所需的功能状态为“已注册”：
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. 一旦区域冗余规模集和网络资源报告为“已注册”，则使用 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 重新注册“计算”和“网络”提供程序，如下所示：

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

若要创建区域冗余规模集，可使用 `-Zone` 参数指定多个区域。 下例跨美国东部 2 区域 1、2、3 创建名为 myScaleSet 的区域冗余规模集配置：

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

如果使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 创建公用 IP 地址或使用 [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer) 创建负载均衡器，则指定 -SKU "Standard" 来创建区域冗余网络资源。 还需要创建网络安全组和规则以允许任意流量。 有关详细信息，请参阅 [Azure 负载均衡器标准概述](../load-balancer/load-balancer-standard-overview.md)。

有关区域冗余规模集和网络资源的完整示例，请参阅[此示例 PowerShell 脚本](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)


## <a name="use-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板
创建使用可用性区域的规模集的过程与适用于 [Linux](virtual-machine-scale-sets-create-template-linux.md) 或 [Windows](virtual-machine-scale-sets-create-template-windows.md) 的入门文章中详述的过程相同。 为使用可用性区域，必须在受支持的 Azure 区域中创建程序集，并[注册可用性区域预览版](http://aka.ms/azenroll)。 在模板中的 *Microsoft.Compute/virtualMachineScaleSets* 资源类型中添加 `zones` 属性并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。

以下示例在美国东部 2 区域 1 中创建名为 myScaleSet 的 Linux 单区域规模集：

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

有关单区域规模集和网络资源的完整示例，请参阅[此示例资源管理器模板](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>区域冗余规模集
若要创建区域冗余规模集，则在 Microsoft.Compute/virtualMachineScaleSets 资源类型的 `zones` 属性中指定多个值。 下例跨美国东部 2 区域 1、2、3 创建名为 myScaleSet 的区域冗余规模集：

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

如果创建公用 IP 地址或负载均衡器，则指定 "sku": { "name": "Standard" }" 属性以创建区域冗余网络资源。 还需要创建网络安全组和规则以允许任意流量。 有关详细信息，请参阅 [Azure 负载均衡器标准概述](../load-balancer/load-balancer-standard-overview.md)。

有关区域冗余规模集和网络资源的完整示例，请参阅[此示例资源管理器模板](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)


## <a name="next-steps"></a>后续步骤
现在，你已在可用性区域中创建了规模集，接下来可以学习如何[在虚拟机规模集上部署应用程序](virtual-machine-scale-sets-deploy-app.md)或[对虚拟机规模集使用自动缩放](virtual-machine-scale-sets-autoscale-overview.md)。
