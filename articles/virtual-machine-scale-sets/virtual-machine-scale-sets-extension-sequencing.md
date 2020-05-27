---
title: 将扩展排序用于 Azure 虚拟机规模集
description: 了解如何在虚拟机规模集上部署多个扩展时对扩展预配进行排序。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121025"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>在虚拟机规模集中对扩展预配进行排序
Azure 虚拟机扩展提供部署后配置和管理、监视、安全性等功能。 生产部署通常结合使用为 VM 实例配置的多个扩展来实现所需效果。

在虚拟机上使用多个扩展时，请务必确保需要相同 OS 资源的扩展并未尝试同时获取这些资源。 一些扩展还依赖其他扩展来提供所需配置，如环境设置和机密。 如果排序不正确，依赖扩展部署可能会失败。

本文详细介绍了如何在虚拟机规模集中对要为 VM 实例配置的扩展进行排序。

## <a name="prerequisites"></a>先决条件
本文假定你熟悉以下内容：
-   Azure 虚拟机[扩展](../virtual-machines/extensions/overview.md)
-   [修改](virtual-machine-scale-sets-upgrade-scale-set.md)虚拟机规模集

## <a name="when-to-use-extension-sequencing"></a>何时使用扩展排序
不强制对规模集执行扩展排序；除非另有指定，否则可以在规模集实例上按任意顺序预配扩展。

例如，如果规模集模型中有两个指定的扩展（扩展 A 和扩展 B），可能会出现以下两种预配顺序之一：
-   扩展 A -> 扩展 B
-   扩展 B -> 扩展 A

如果应用程序要求扩展 A 始终先于扩展 B 预配，应使用本文中所述的扩展排序。 使用扩展排序后，现在只会出现以下一种顺序：
-   扩展 A -> 扩展 B

任何未指定定义的预配顺序的扩展都可以随时预配（包括定义的顺序之前、之后或期间）。 扩展排序仅指定一个特定扩展在另一个特定扩展之后预配。 它不影响模型中定义的其他任何扩展的预配。

例如，如果规模集模型中有三个指定的扩展（扩展 A、扩展 B 和扩展 C），并且扩展 C 设置为在扩展 A 之后预配，可能会出现以下三种预配顺序之一：
-   扩展 A -> 扩展 C -> 扩展 B
-   扩展 B -> 扩展 A -> 扩展 C
-   扩展 A -> 扩展 B -> 扩展 C

如果需要确保在执行定义的扩展顺序时不预配其他任何扩展，建议对规模集模型中的所有扩展进行排序。 在上面的示例中，可以将扩展 B 设置为在扩展 C 之后预配，这样就只能出现以下一种顺序：
-   扩展 A -> 扩展 C -> 扩展 B


## <a name="how-to-use-extension-sequencing"></a>如何使用扩展排序
若要对扩展预配进行排序，必须将规模集模型中的扩展定义更新为，包括接受扩展名数组的属性“provisionAfterExtensions”。 必须在规模集模型中完全定义属性数组值中提到的扩展。

### <a name="template-deployment"></a>模板部署
下面的示例定义了一个模板，其中规模集有三个扩展（扩展 A、扩展 B 和扩展 C），这样扩展就能按以下顺序预配：
-   扩展 A -> 扩展 B -> 扩展 C

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

由于属性“provisionAfterExtensions”接受扩展名数组，因此可以将上面的示例修改为，扩展 C 在扩展 A 和扩展 B 之后预配，但无需对扩展 A 和扩展 B 进行排序。 以下模板可用于实现此方案：

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
下面的示例将名为“扩展 C”的新扩展添加到规模集模型中。 扩展 C 依赖扩展 A 和扩展 B，这些扩展已在规模集模型中定义。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

如果规模集模型中之前已定义扩展 C，并且现在要添加它的依赖项，可以执行 `PATCH` 来编辑已部署的扩展属性。

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
对现有规模集实例的更改在下一次[升级](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)时应用。

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet 将应用程序运行状况扩展添加到规模集模型定义中。 必须使用 Az PowerShell 1.2.0 或更高版本，才能执行扩展排序。

下面的示例将[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)添加到 `extensionProfile`（位于基于 Windows 的规模集的规模集模型中）。 应用程序运行状况扩展在规模集中已定义的[自定义脚本扩展](../virtual-machines/extensions/custom-script-windows.md)后预配。

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss 扩展集](/cli/azure/vmss/extension#az-vmss-extension-set)将应用程序运行状况扩展添加到规模集模型定义中。 必须使用 Azure CLI 2.0.55 或更高版本，才能执行扩展排序。

下面的示例将[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)添加到基于 Windows 的规模集的规模集模型中。 应用程序运行状况扩展在规模集中已定义的[自定义脚本扩展](../virtual-machines/extensions/custom-script-windows.md)后预配。

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>疑难解答

### <a name="not-able-to-add-extension-with-dependencies"></a>无法添加有依赖项的扩展？
1. 请确保 provisionAfterExtensions 中指定的扩展已在规模集模型中定义。
2. 请确保未引入循环依赖。 例如，禁止使用以下顺序：扩展 A -> 扩展 B -> 扩展 C -> 扩展 A
3. 请确保所依赖的任何扩展在扩展“属性”下有“设置”属性。 例如，如果需要在扩展 A 之后预配扩展 B，扩展 A 必须在扩展 A“属性”下有“设置”字段。 如果扩展不强制要求任何必需设置，可以指定空的“设置”属性。

### <a name="not-able-to-remove-extensions"></a>无法删除扩展？
请确保要删除的扩展未在其他任何扩展的 provisionAfterExtensions 下列出。

## <a name="next-steps"></a>后续步骤
了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
