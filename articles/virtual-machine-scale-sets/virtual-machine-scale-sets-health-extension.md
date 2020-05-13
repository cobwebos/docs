---
title: 使用 Azure 虚拟机规模集的应用程序运行状况扩展
description: 了解如何使用应用程序运行状况扩展监视部署在虚拟机规模集上的应用程序的运行状况。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 4710d03c4d5b2f2679a0d6b65f38ec584f9a056c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124102"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>配合使用虚拟机规模集和应用程序运行状况扩展
监视应用程序的运行状况是管理和升级部署的重要信号。 Azure 虚拟机规模集支持[滚动升级](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)（包括[自动 OS-image 升级](virtual-machine-scale-sets-automatic-upgrade.md)），其依赖对各实例的运行状况监视来升级部署。 你还可以使用运行状况扩展来监视规模集中每个实例的应用程序运行状况，并使用[自动实例修复](virtual-machine-scale-sets-automatic-instance-repairs.md)执行实例修复。

本文介绍如何使用应用程序运行状况扩展监控部署在虚拟机规模集上的应用程序的运行状况。

## <a name="prerequisites"></a>先决条件
本文假定你熟悉以下内容：
-   Azure 虚拟机[扩展](../virtual-machines/extensions/overview.md)
-   [修改](virtual-machine-scale-sets-upgrade-scale-set.md)虚拟机规模集

## <a name="when-to-use-the-application-health-extension"></a>何时使用应用程序运行状况扩展
应用程序运行状况扩展部署在虚拟机规模集实例中，并从规模集实例中报告 VM 的运行状况。 可以配置扩展，以探测应用程序终结点并更新该实例上应用程序的状态。 Azure 会检查此实例状态，以确定实例是否符合升级操作的条件。

因为扩展从 VM 中报告运行状况，所以扩展可在无法利用外部探测（例如使用自定义 Azure 负载均衡器[探测](../load-balancer/load-balancer-custom-probe-overview.md)的应用程序运行状况探测）的情况下使用。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示应用程序运行状况扩展的架构。 扩展至少需要 "tcp"、"http" 或 "https" 请求，分别具有关联的端口或请求路径。

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
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | string |
| type | `ApplicationHealthLinux` (Linux)、`ApplicationHealthWindows` (Windows) | string |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>设置

| 名称 | 值/示例 | 数据类型
| ---- | ---- | ----
| protocol | `http` 或 `https` 或 `tcp` | string |
| port | 如果协议为 `http` 或 `https` ，则为可选，协议为时是必需的`tcp` | int |
| requestPath | 当协议为 `http` 或时 `https` ，不允许使用`tcp` | string |

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

下面的示例将应用程序运行状况扩展添加到基于 Linux 的规模集的规模集模型中。

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
extension.json 文件内容。

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>疑难解答
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
