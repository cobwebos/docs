---
title: 对 Azure 虚拟机规模集使用自定义横向缩减策略
description: 了解如何对使用自动缩放配置管理实例计数的 Azure 虚拟机规模集使用自定义横向缩减策略
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 479bbfaf8468329cd515799e5822497df2bb4c1d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125156"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>对 Azure 虚拟机规模集使用自定义横向缩减策略

可根据一系列指标（包括平台指标和用户定义的自定义指标）横向扩展或缩减虚拟机规模集部署。 在规模集工作负荷的演变过程中，横向扩展可根据规模集模型创建新的虚拟机，而横向缩减会影响正在运行的采用不同配置和/或功能的虚拟机。 

横向缩减策略功能提供三项横向缩减配置，可让用户配置虚拟机的横向缩减顺序： 

1. 默认
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Default 横向缩减策略

默认情况下，虚拟机规模集会应用此策略来确定要横向缩减的实例。 使用 *Default* 策略时，将按以下顺序选择要横向缩减的 VM：

1. 跨可用性区域均衡虚拟机（如果规模集部署在区域配置中）
2. 跨容错域均衡虚拟机（尽力而为）
3. 删除实例 ID 最大的虚拟机

如果用户只想遵循默认排序，他们无需指定横向缩减策略。

请注意，跨可用性区域或容错域的均衡不会在可用性区域或容错域之间移动实例。 均衡的实现方式是从不均衡的可用性区域或容错域中删除虚拟机，直到虚拟机的分布均衡。

### <a name="newestvm-scale-in-policy"></a>NewestVM 横向缩减策略

此策略在跨可用性区域均衡 VM 之后删除规模集中创建的最新虚拟机（适用于区域部署）。 若要启用此策略，需要对虚拟机规模集模型进行配置更改。

### <a name="oldestvm-scale-in-policy"></a>OldestVM 横向缩减策略

此策略在跨可用性区域均衡 VM 之后删除规模集中创建的最旧虚拟机（适用于区域部署）。 若要启用此策略，需要对虚拟机规模集模型进行配置更改。

## <a name="enabling-scale-in-policy"></a>启用横向缩减策略

横向缩减策略在虚拟机规模集模型中定义。 如以上部分中所述，使用“NewestVM”和“OldestVM”策略时需要横向缩减策略定义。 如果在规模集模型中找不到横向缩减策略定义，虚拟机规模集将自动使用“Default”横向缩减策略。 

可通过以下方式在虚拟机规模集模型上定义横向缩减策略：

### <a name="azure-portal"></a>Azure 门户
 
以下步骤定义了创建新规模集时的横向缩减策略。 
 
1.  转到“虚拟机规模集”。
1. 选择“+ 添加”，创建新的规模集  。
1. 转到“缩放”选项卡。  
1. 找到“横向缩减策略”部分。 
1. 从下拉列表中选择横向缩减策略。
1. 创建完新的规模集后，选择“查看 + 创建”按钮  。

### <a name="using-api"></a>使用 API

使用 API 2019-03-01 对虚拟机规模集执行 PUT：

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

创建一个资源组，然后创建一个新的规模集，并将横向缩减策略设置为 *OldestVM*。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下示例添加了创建新规模集时的横向缩减策略。 首先创建一个资源组，然后创建一个新的规模集，将横向缩减策略设置为 *OldestVM*。 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>使用模板

在模板中的“properties”下添加以下代码：

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

以上代码块指定在触发横向缩减时，虚拟机规模集将删除区域均衡的规模集中的最旧 VM（通过自动缩放或手动删除）。

当虚拟机规模集未进行区域均衡时，规模集会先删除跨不均衡区域的 VM。 在不均衡的区域中，规模集将使用上面指定的横向缩减策略来确定要横向缩减哪个 VM。 在这种情况下，在不均衡的区域中，规模集将选择删除该区域中的最旧 VM。

对于非区域性虚拟机规模集，策略会选择删除整个规模集中的最旧 VM。

在上述横向缩减策略中使用“NewestVM”时，相同的过程适用。

## <a name="modifying-scale-in-policies"></a>修改横向缩减策略

修改横向缩减策略的过程与应用横向缩减策略的过程相同。 例如，如果在上述示例中你要将策略从“OldestVM”更改为“NewestVM”，可执行以下操作：

### <a name="azure-portal"></a>Azure 门户

可以通过 Azure 门户修改现有规模集的横向缩减策略。 
 
1. 在现有的虚拟机规模集中，从左侧菜单中选择“缩放”  。
1. 选择“横向缩减策略”  选项卡。
1. 从下拉列表中选择横向缩减策略。
1. 完成后，选择“保存”  。 

### <a name="using-api"></a>使用 API

使用 API 2019-03-01 对虚拟机规模集执行 PUT：

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

更新现有规模集的横向缩减策略：

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下面是一个示例，演示了如何更新现有规模集的横向缩减策略： 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>使用模板

在模板中的“properties”下，按如下所示修改模板并重新部署： 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

如果你决定将“NewestVM”更改为“Default”或“OldestVM”，可应用相同的过程

## <a name="instance-protection-and-scale-in-policy"></a>实例保护和横向缩减策略

虚拟机规模集提供两种类型的[实例保护](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)：

1. 防止进行横向缩减
2. 防止进行规模集操作

无论应用了哪个横向缩减策略，都不会通过横向缩减操作删除受保护的虚拟机。 例如，如果防止横向缩减 VM_0（规模集中的最旧 VM），并为规模集启用了“OldestVM”横向缩减策略，则不考虑横向缩减 VM_0，尽管它是规模集中的最旧 VM。 

无论对规模集启用了哪个横向缩减策略，用户都随时可以手动删除受保护的虚拟机。 

## <a name="usage-examples"></a>用法示例 

以下示例演示了当触发横向缩减事件时，虚拟机规模集如何选择要删除的 VM。 假设实例 ID 最大的虚拟机为规模集中的最新 VM，实例 ID 最小的 VM 为规模集中的最旧 VM。 

### <a name="oldestvm-scale-in-policy"></a>OldestVM 横向缩减策略

| 事件                 | 区域 1 中的实例 ID  | 区域 2 中的实例 ID  | 区域 3 中的实例 ID  | 横向缩减选择                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| 初始               | 3、4、5、10            | 2、6、9、11            | 1、7、8                |                                                                                                                                  |
| 横向缩减              | 3、4、5、10            | ***2***、6、9、11      | 1、7、8                | 尽管区域 3 包含最旧的 VM，但仍会在区域 1 和 2 之间选择。 删除区域 2 中的 VM2，因为它是该区域中的最旧 VM。   |
| 横向缩减              | ***3***、4、5、10      | 6、9、11               | 1、7、8                | 尽管区域 3 包含最旧的 VM，但仍会选择区域 1。 删除区域 1 中的 VM3，因为它是该区域中的最旧 VM。                  |
| 横向缩减              | 4、5、10               | 6、9、11               | ***1***、7、8          | 区域已均衡。 删除区域 3 中的 VM1，因为它是规模集中的最旧 VM。                                               |
| 横向缩减              | ***4***、5、10         | 6、9、11               | 7, 8                   | 在区域 1 和区域 2 之间选择。 删除区域 1 中的 VM4，因为它是两个区域中的最旧 VM。                              |
| 横向缩减              | 5、10                  | ***6***、9、11         | 7, 8                   | 尽管区域 1 包含最旧的 VM，但仍会选择区域 2。 删除区域 1 中的 VM6，因为它是该区域中的最旧 VM。                    |
| 横向缩减              | ***5***、10            | 9、11                  | 7, 8                   | 区域已均衡。 删除区域 1 中的 VM5，因为它是规模集中的最旧 VM。                                                |

对于非区域性虚拟机规模集，策略会选择删除整个规模集中的最旧 VM。 将跳过任何“受保护”VM 的删除。

### <a name="newestvm-scale-in-policy"></a>NewestVM 横向缩减策略

| 事件                 | 区域 1 中的实例 ID  | 区域 2 中的实例 ID  | 区域 3 中的实例 ID  | 横向缩减选择                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| 初始               | 3、4、5、10            | 2、6、9、11            | 1、7、8                |                                                                                                                                  |
| 横向缩减              | 3、4、5、10            | 2、6、9、***11***      | 1、7、8                | 在区域 1 和 2 之间选择。 删除区域 2 中的 VM11，因为它是两个区域中的最新 VM。                                |
| 横向缩减              | 3、4、5、***10***      | 2、6、9                | 1、7、8                | 选择区域 1，因为它包含的 VM 比其他两个区域要多。 删除区域 1 中的 VM10，因为它是该区域中的最新 VM。          |
| 横向缩减              | 3、4、5                | 2、6、***9***          | 1、7、8                | 区域已均衡。 删除区域 2 中的 VM9，因为它是规模集中的最新 VM。                                                |
| 横向缩减              | 3、4、5                | 2、6                   | 1、7、***8***          | 在区域 1 和区域 3 之间选择。 删除区域 3 中的 VM8，因为它是该区域中的最新 VM。                                      |
| 横向缩减              | 3、4、***5***          | 2、6                   | 1, 7                   | 尽管区域 3 包含最新的 VM，但仍会选择区域 1。 删除区域 1 中的 VM5，因为它是该区域中的最新 VM。                    |
| 横向缩减              | 3、4                   | 2、6                   | 1、***7***             | 区域已均衡。 删除区域 3 中的 VM7，因为它是规模集中的最新 VM。                                                |

对于非区域性虚拟机规模集，策略会选择删除整个规模集中的最新 VM。 将跳过任何“受保护”VM 的删除。 

## <a name="troubleshoot"></a>故障排除

1. 启用横向缩减策略失败。如果收到“错误的请求”错误并出现错误消息“在 'properties' 类型的对象中找不到成员 'scaleInPolicy'”，请检查虚拟机规模集使用的 API 版本。 此功能需要 API 版本 2019-03-01 或更高版本。

2. 错误地选择了要横向缩减的 VM。请参阅上述示例。 如果虚拟机规模集是区域性部署，则会先对不均衡的区域应用横向缩减策略，并在区域均衡后，对整个规模集应用该策略。 如果横向缩减的顺序与上述示例不一致，请咨询虚拟机规模集团队进行故障排除。

## <a name="next-steps"></a>后续步骤

了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
