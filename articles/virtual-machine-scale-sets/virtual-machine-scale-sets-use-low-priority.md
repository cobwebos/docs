---
title: 创建使用低优先级 VM（预览版）的 Azure 规模集 | Microsoft Docs
description: 了解如何创建使用经济型低优先级 VM 的 Azure 虚拟机规模集
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 5c0726ea0da288d5306e28b101e4d3b59605b443
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894888"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>规模集中的低优先级 VM（预览版）

在规模集中使用低优先级 VM 可以利用未使用的容量，同时大幅降低成本。 每当 Azure 需要回收容量时，Azure 基础结构就会逐出低优先级 VM。 因此，低优先级 VM 非常适合可以处理服务中断的工作负荷，例如批处理作业、开发/测试环境、大型计算工作负荷，等等。

可用的未用容量根据大小、区域、时间等因素而异。 在规模集中部署低优先级 VM 时，如果有可用的容量，则 Azure 会分配 VM，但这些 VM 没有 SLA 的保障。 低优先级规模集部署在单个容错域中，不提供高可用性保证。

## <a name="eviction-policy"></a>逐出策略

如果创建低优先级规模集，可将收回策略设置为“解除分配”（默认值）或“删除”。 

“解除分配”策略可将已收回的 VM 移到已停止解除分配的状态，以允许重新部署收回的实例。 但是，不保证分配将成功。 已解除分配的 VM 将计入规模集实例配额，基础磁盘仍会产生费用。 

如果希望在收回低优先级规模集中的 VM 后将其删除，可以将收回策略设置为删除。 将逐出策略设置为删除后，可以通过增大规模集实例计数属性来创建新的 VM。 逐出的 VM 会连同其基础磁盘一起删除，因此可以避免存储费用。 还可以使用规模集的自动缩放功能来自动尝试补偿逐出的 VM，但是，无法保证分配成功。 我们建议仅在将逐出策略设置为删除时，才对低优先级规模集使用自动缩放功能，以免产生磁盘费用和达到配额限制。 

> [!NOTE]
> 在预览期，可以使用 [Azure 门户](#use-the-azure-portal)和 [Azure 资源管理器模板](#use-azure-resource-manager-templates)设置收回策略。 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>在规模集中部署低优先级 VM

若要在规模集中部署低优先级 VM，可将新的 *Priority* 标志设置为 *Low*。 这样，规模集中的所有 VM 将设置为低优先级。 若要创建包含低优先级 VM 的规模集，请使用以下方法之一：
- [Azure 门户](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure 资源管理器模板](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

创建使用低优先级 VM 的规模集的过程与[入门文章](quick-create-portal.md)中详述的过程一样。 如果要部署规模集，可以选择设置低优先级标志和收回策略：![使用低优先级 VM 创建规模集](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli-20"></a>使用 Azure CLI 2.0

创建包含低优先级 VM 的规模集的过程与[入门文章](quick-create-cli.md)中详述的过程相同。 只需将“--Priority”参数添加到 cli 调用并将其设置为 *Low*，如以下示例所示：

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

创建包含低优先级 VM 的规模集的过程与[入门文章](quick-create-powershell.md)中详述的过程相同。
只需将“--Priority”参数添加到 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) 并将其设置为 *Low*，如以下示例所示：

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

创建使用低优先级 VM 的规模集的过程与适用于 [Linux](quick-create-template-linux.md) 或 [Windows](quick-create-template-windows.md) 的入门文章中详述的过程相同。 在模板中将“priority”属性添加到 *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* 资源类型，并指定*Low* 作为值。 请务必使用 2018-03-01 或更高版本的 API。 

若要将逐出策略设置为删除，请添加“evictionPolicy”参数并将其设置为 *delete*。

以下示例在 *West Central US*（美国中西部）位置创建名为 *myScaleSet* 的 Linux 低优先级规模集，逐出时会删除该规模集中的 VM：

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
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
## <a name="faq"></a>常见问题

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>是否可以将现有的规模集转换为低优先级规模集？
不可以，低优先级标志仅在创建时受支持。

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>是否可以创建一个同时包含常规 VM 和低优先级 VM 的规模集？
不可以，一个规模集只能支持一种优先级类型。

### <a name="how-is-quota-managed-for-low-priority-vms"></a>如何管理低优先级 VM 的配额？
低优先级 VM 和常规 VM 共享同一配额池。 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>是否可以结合使用自动缩放与低优先级规模集？
可以，可在低优先级规模集中设置自动缩放规则。 如果已收回 VM，可尝试通过自动缩放创建新的低优先级 VM。 但记住，不保证会创建成功。 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>自动缩放是否可用于这两种收回策略（解除分配和删除）？
建议使用自动缩放时将收回策略设置为删除。 这是因为解除分配的实例将计入规模集的容量计数。 如果使用自动缩放，由于实例已解除分配且已收回，实例很可能会快速达到目标数。 

## <a name="next-steps"></a>后续步骤
创建包含低优先级 VM 的规模集后，请尝试部署我们的[使用低优先级 VM 的自动缩放模板](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri)。

有关价格详细信息，请查看[虚拟机规模集定价页](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。