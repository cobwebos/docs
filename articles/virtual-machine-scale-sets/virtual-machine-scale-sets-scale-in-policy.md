---
title: 使用 Azure 虚拟机规模集的自定义扩展策略
description: 了解如何在 Azure 虚拟机规模集（使用自动缩放配置来管理实例计数）中使用自定义的扩展策略
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919832"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>使用 Azure 虚拟机规模集的自定义扩展策略

虚拟机规模集部署可根据指标（包括平台和用户定义的自定义指标）进行横向扩展或缩小。 虽然横向扩展基于规模集模型创建新的虚拟机，但放大规模会影响在规模集工作负载变化时可能具有不同配置和/或功能的虚拟机。 

"扩展策略" 功能为用户提供了一种方法，用于通过三个扩展配置来配置虚拟机的缩小顺序： 

1. 默认
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>默认的扩展策略

默认情况下，虚拟机规模集会应用此策略，以确定将在其中进行缩放的实例。 采用*默认*策略时，按以下顺序为按比例选择 vm：

1. 跨可用性区域平衡虚拟机（如果规模集部署在区域配置中）
2. 跨容错域平衡虚拟机（最大努力）
3. 删除实例 ID 最高的虚拟机

如果用户只想按下默认排序，则用户无需指定扩展策略。

请注意，跨可用性区域或容错域的均衡不会跨可用性区域或容错域移动实例。 通过删除不平衡的可用性区域或容错域中的虚拟机来实现均衡，直到虚拟机的分发变为平衡。

### <a name="newestvm-scale-in-policy"></a>NewestVM 扩展策略

此策略将删除规模集中的最新创建的虚拟机，在跨可用性区域（适用于区域部署）平衡 Vm 之后。 若要启用此策略，需要对虚拟机规模集模型进行配置更改。

### <a name="oldestvm-scale-in-policy"></a>OldestVM 扩展策略

此策略将删除规模集中最早创建的虚拟机，并在跨可用性区域（适用于区域部署）平衡 Vm 之后。 若要启用此策略，需要对虚拟机规模集模型进行配置更改。

## <a name="enabling-scale-in-policy"></a>启用扩展策略

在虚拟机规模集模型中定义了扩展策略。 如以上部分所述，使用 "NewestVM" 和 "OldestVM" 策略时需要进行扩展策略定义。 如果规模集模型中找不到扩展的策略定义，则虚拟机规模集将自动使用 "默认" 扩展策略。 

可以通过以下方式在虚拟机规模集模型上定义扩展策略：

### <a name="azure-portal"></a>Azure 门户
 
以下步骤定义了创建新规模集时的 "扩展策略"。 
 
1. 请参阅**虚拟机规模集**。
1. 选择 " **+ 添加**" 创建新的规模集。
1. 中转到 "**缩放**" 选项卡。 
1. 找到 "**扩展策略**" 部分。
1. 从下拉范围中选择 "扩展策略"。
1. 创建新规模集后，请选择 "**查看 + 创建**" 按钮。

### <a name="using-api"></a>使用 API

使用 API 2019-03-01 对虚拟机规模集执行 PUT 操作：

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

创建一个资源组，然后创建一个将扩展策略设置为*OldestVM*的新规模集。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下面的示例在创建新规模集时添加一个扩展策略。 首先，创建一个资源组，然后使用 " *OldestVM*" 作为 "扩展" 策略创建新的规模集。 

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

在模板中的 "属性" 下，添加以下内容：

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

上述块指定虚拟机规模集将在触发扩展时（通过自动缩放或手动删除）删除区域平衡规模集中最早的 VM。

当虚拟机规模集未进行区域平衡时，规模集将首先删除不均衡区域内的 Vm。 在不均衡区域中，规模集将使用上面指定的扩展策略来确定要在哪个 VM 中进行缩放。 在这种情况下，在不均衡区域中，规模集将在要删除的该区域中选择最早的 VM。

对于非区域虚拟机规模集，策略将选择要删除的规模集内最早的 VM。

在上述范围内的 "NewestVM" 中使用 "" 时，将使用相同的过程。

## <a name="modifying-scale-in-policies"></a>修改扩展策略

修改扩展策略后跟应用扩展策略的过程相同。 例如，如果在上述示例中，你想要将策略从 "OldestVM" 更改为 "NewestVM"，则可以通过以下方法执行此操作：

### <a name="azure-portal"></a>Azure 门户

可以通过 Azure 门户修改现有规模集的扩展策略。 
 
1. 在现有的虚拟机规模集中，从左侧菜单中选择 "**缩放**"。
1. 选择 "**扩展策略**" 选项卡。
1. 从下拉范围中选择 "扩展策略"。
1. 完成后，请选择 "**保存**"。 

### <a name="using-api"></a>使用 API

使用 API 2019-03-01 对虚拟机规模集执行 PUT 操作：

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

更新现有规模集的扩展策略：

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下面是更新现有规模集的扩展策略的示例： 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>使用模板

在模板中的 "属性" 下，按如下所示修改模板并重新部署： 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

如果决定将 "NewestVM" 更改为 "Default" 或 "OldestVM"，则会应用相同的过程

## <a name="instance-protection-and-scale-in-policy"></a>实例保护和扩展策略

虚拟机规模集提供两种类型的[实例保护](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)：

1. 防止缩小
2. 防止规模集操作

受保护的虚拟机不会通过缩小操作而删除，无论应用的是扩展策略是什么。 例如，如果 VM_0 （规模集中最早的 VM）受到了扩展保护，并且规模集启用了 "OldestVM" 扩展策略，则不会将 VM_0 视为在中进行缩放，即使它是规模集中最早的 VM 也是如此。 

无论规模集上启用了扩展策略，用户都可以随时手动删除受保护的虚拟机。 

## <a name="usage-examples"></a>用法示例 

下面的示例演示了虚拟机规模集如何选择在触发大规模事件时要删除的 Vm。 具有最高实例 Id 的虚拟机假设为规模集中的最新 Vm，而具有最小实例 Id 的 Vm 被认为是规模集中最早的 Vm。 

### <a name="oldestvm-scale-in-policy"></a>OldestVM 扩展策略

| 事件                 | 月中的实例 Id  | 区域1中的实例 Id  | 区域1月中的实例 Id  | 放大选择                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| 初始               | 3、4、5、10            | 2，6，9，11            | 1、7、8                |                                                                                                                                  |
| 缩小              | 3、4、5、10            | ***2***，6，9，11      | 1、7、8                | 如果区域3具有最早的 VM，请选择 "区域 1" 和 "2"。 从区域2中删除 VM2，因为它是该区域中最旧的 VM。   |
| 缩小              | ***3***、4、5、10      | 6，9，11               | 1、7、8                | 即使区域3具有最早的 VM，也请选择 "区域 1"。 从区域1中删除 VM3，因为它是该区域中最旧的 VM。                  |
| 缩小              | 4、5、10               | 6，9，11               | ***1***、7、8          | 区域已平衡。 删除区域3中的 VM1，因为它是规模集中最早的 VM。                                               |
| 缩小              | ***4***、5、10         | 6，9，11               | 7, 8                   | 选择区域1和区域2。 删除区域1中的 VM4，因为它是跨两个区域的最旧 VM。                              |
| 缩小              | 5, 10                  | ***6***，9，11         | 7, 8                   | 即使区域1具有最早的 VM，也请选择 "区域 2"。 删除区域1中的 VM6，因为它是该区域中最旧的 VM。                    |
| 缩小              | ***5***、10            | 9，11                  | 7, 8                   | 区域已平衡。 删除区域1中的 VM5，因为它是规模集中最早的 VM。                                                |

对于非区域虚拟机规模集，策略将选择要删除的规模集内最早的 VM。 将跳过任何 "受保护的" VM 进行删除。

### <a name="newestvm-scale-in-policy"></a>NewestVM 扩展策略

| 事件                 | 月中的实例 Id  | 区域1中的实例 Id  | 区域1月中的实例 Id  | 放大选择                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| 初始               | 3、4、5、10            | 2，6，9，11            | 1、7、8                |                                                                                                                                  |
| 缩小              | 3、4、5、10            | 2，6，9， ***11***      | 1、7、8                | 选择区域1和2。 从区域2中删除 VM11，因为它是跨两个区域的最新 VM。                                |
| 缩小              | 3、4、5、 ***10***      | 2、6、9                | 1、7、8                | 选择 "区域 1"，因为它具有比其他两个区域更多的 Vm。 从区域1中删除 VM10，因为它是该区域中的最新 VM。          |
| 缩小              | 3, 4, 5                | 2、6、 ***9***          | 1、7、8                | 区域已平衡。 删除区域2中的 PG2，因为它是规模集中最新的 VM。                                                |
| 缩小              | 3, 4, 5                | 2、6                   | 1、7、 ***8***          | 选择区域1和区域3。 删除区域3中的 VM8，因为它是该区域中的最新 VM。                                      |
| 缩小              | 3、4、 ***5***          | 2、6                   | 1, 7                   | 即使区域3具有最新的 VM，也请选择 "区域 1"。 删除区域1中的 VM5，因为它是该区域中的最新 VM。                    |
| 缩小              | 3、4                   | 2、6                   | 1、 ***7***             | 区域已平衡。 删除区域3中的 VM7，因为它是规模集中最新的 VM。                                                |

对于非区域虚拟机规模集，策略选择要删除的规模集内的最新 VM。 将跳过任何 "受保护的" VM 进行删除。 

## <a name="troubleshoot"></a>故障排除

1. 如果在 "properties" 类型的对象上出现 "BadRequest" 错误，并显示错误消息 "找不到成员 ' scaleInPolicy '"，则无法启用 scaleInPolicy，然后检查用于虚拟机规模集的 API 版本。 此功能需要 API 版本2019-03-01 或更高版本。

2. 用于扩展的 Vm 选择错误，请参阅上述示例。 如果虚拟机规模集是一个区域部署，则先将扩展策略应用于不均衡区域，然后在规模集中对其进行区域平衡。 如果按比例调整的顺序与上述示例不一致，请使用虚拟机规模集团队引发查询进行故障排除。

## <a name="next-steps"></a>后续步骤

了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。