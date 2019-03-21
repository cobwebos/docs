---
title: 配合使用 Azure 虚拟机规模集和应用程序运行状况扩展 | Microsoft Docs
description: 了解如何使用应用程序运行状况扩展监视部署在虚拟机规模集上的应用程序的运行状况。
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: d1cff1011e190e5fbb2874657cbdfbdc68bde0c0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084389"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>配合使用虚拟机规模集和应用程序运行状况扩展
监视应用程序的运行状况是管理和升级部署的重要信号。 Azure 虚拟机规模集支持[滚动升级](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)（包括[自动 OS-image 升级](virtual-machine-scale-sets-automatic-upgrade.md)），其依赖对各实例的运行状况监视来升级部署。

本文介绍如何使用应用程序运行状况扩展监控部署在虚拟机规模集上的应用程序的运行状况。

## <a name="prerequisites"></a>必备组件
本文假定你熟悉以下内容：
-   Azure 虚拟机[扩展](../virtual-machines/extensions/overview.md)
-   [修改](virtual-machine-scale-sets-upgrade-scale-set.md)虚拟机规模集

## <a name="when-to-use-the-application-health-extension"></a>何时使用应用程序运行状况扩展
应用程序运行状况扩展部署在虚拟机规模集实例中，并从规模集实例中报告 VM 的运行状况。 可以配置扩展，以探测应用程序终结点并更新该实例上应用程序的状态。 Azure 会检查此实例状态，以确定实例是否符合升级操作的条件。

因为扩展从 VM 中报告运行状况，所以扩展可在无法利用外部探测（例如使用自定义 Azure 负载均衡器[探测](../load-balancer/load-balancer-custom-probe-overview.md)的应用程序运行状况探测）的情况下使用。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示应用程序运行状况扩展的架构。 扩展至少需要“tcp”或“http”请求，且各自具有相关的端口或请求路径。

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | 日期 |
| 发布者 | `Microsoft.ManagedServices` | 字符串 |
| type | `ApplicationHealthLinux` (Linux)、`ApplicationHealthWindows` (Windows) | 字符串 |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>设置

| 名称 | 值/示例 | 数据类型
| ---- | ---- | ----
| 协议 | `http` 或 `tcp` | 字符串 |
| port | 协议为 `http` 时为可选，协议为 `tcp` 时为必需 | int |
| requestPath | 协议为 `http` 时为必需，协议为 `tcp` 时为不允许 | 字符串 |

## <a name="deploy-the-application-health-extension"></a>部署应用程序运行状况扩展
可以使用多种方法将应用程序运行状况扩展部署到规模集，如下面的示例所示。

### <a name="rest-api"></a>REST API

下面的示例将应用程序运行状况扩展（名为 myHealthExtension）添加到 extensionProfile（位于基于 Windows 的规模集的规模集模型中）。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
使用 `PATCH` 编辑已部署的扩展。

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet 将应用程序运行状况扩展添加到规模集模型定义中。

下面的示例将应用程序运行状况扩展添加到基于 Windows 的规模集的规模集模型的 `extensionProfile` 中。 该示例使用新的 Az PowerShell 模块。

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
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

使用 [az vmss 扩展集](/cli/azure/vmss/extension#az-vmss-extension-set)将应用程序运行状况扩展添加到规模集模型定义中。

下面的示例将应用程序运行状况扩展添加到基于 Windows 的规模集的规模集模型中。

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>故障排除
扩展执行输出将记录到在以下目录中发现的文件：

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

日志还会定期捕获应用程序运行状况状态。

## <a name="next-steps"></a>后续步骤
了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
