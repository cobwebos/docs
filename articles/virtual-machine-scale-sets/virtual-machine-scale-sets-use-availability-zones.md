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
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>创建使用可用性区域（预览版）的虚拟机规模集
若要防止虚拟机规模集发生数据中心级故障，可以在可用性区域中创建规模集。 支持可用性区域的 Azure 区域最少具有三个单独的区域，每个都有其自己的独立电源、网络和冷却设备。 有关详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

若要使用可用性区域，必须在[受支持的 Azure 区域](../availability-zones/az-overview.md#regions-that-support-availability-zones)中创建规模集。 可以使用下列方法之一创建使用可用性区域的规模集：

- [Azure portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure 资源管理器模板](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>使用 Azure 门户
创建使用可用性区域的规模集的过程与[入门文章](virtual-machine-scale-sets-create-portal.md)中详述的过程相同。 选择一个受支持的 Azure 区域后，可以在可用性区域之一中创建规模集，如以下示例中所示：

![在单个可用性区域中创建规模集](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

将在你指定的单个区域中创建规模集和支持资源，例如 Azure 负载均衡器和公共 IP 地址。


## <a name="use-the-azure-cli-20"></a>使用 Azure CLI 2.0
创建使用可用性区域的规模集的过程与[入门文章](virtual-machine-scale-sets-create-cli.md)中详述的过程相同。 若要使用可用性区域，必须在受支持的 Azure 区域中创建规模集。 在 [az vmss create](/cli/azure/vmss#az_vmss_create) 命令中添加 `--zones` 参数并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。 以下示例在区域 *1* 中创建名为 *myScaleSet* 的规模集：

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

在指定的区域中创建和配置所有规模集资源和 VM 需要几分钟时间。


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
创建使用可用性区域的规模集的过程与[入门文章](virtual-machine-scale-sets-create-powershell.md)中详述的过程相同。 若要使用可用性区域，必须在受支持的 Azure 区域中创建规模集。 在 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) 命令中添加 `-Zone` 参数并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。 以下示例在 *East US 2* 区域 *1*中创建名为 *vmssConfig* 的规模集配置：

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

若要创建实际的规模集，请执行[入门文章](virtual-machine-scale-sets-create-powershell.md)中详述的附加步骤。


## <a name="use-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板
创建使用可用性区域的规模集的过程与适用于 [Linux](virtual-machine-scale-sets-create-template-linux.md) 或 [Windows](virtual-machine-scale-sets-create-template-windows.md) 的入门文章中详述的过程相同。 若要使用可用性区域，必须在受支持的 Azure 区域中创建规模集。 在模板中的 *Microsoft.Compute/virtualMachineScaleSets* 资源类型中添加 `zones` 属性并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。 以下示例在 *East US 2* 区域 *1*中创建名为 *myScaleSet* 的 Linux 规模集：

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

若要创建实际的规模集，请执行适用于 [Linux](virtual-machine-scale-sets-create-template-linux.md) 或 [Windows](virtual-machine-scale-sets-create-template-windows.md) 的入门文章中详述的附加步骤。


## <a name="next-steps"></a>后续步骤
现在，你已在可用性区域中创建了规模集，接下来可以学习如何[在虚拟机规模集上部署应用程序](virtual-machine-scale-sets-deploy-app.md)或[对虚拟机规模集使用自动缩放](virtual-machine-scale-sets-autoscale-overview.md)。
