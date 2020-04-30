---
title: 使用邻近位置组
description: 了解如何在 Azure 中为虚拟机创建和使用邻近感应布局组。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: f89b28e7a3c29e45efa2796788e27325c01d7098
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759251"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>使用 Azure CLI 将 Vm 部署到邻近位置组

若要尽可能降低 Vm，请尽可能降低延迟，然后将它们部署到[邻近的放置组](co-location.md#proximity-placement-groups)。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。


## <a name="create-the-proximity-placement-group"></a>创建邻近位置组
使用[`az ppg create`](/cli/azure/ppg#az-ppg-create)创建邻近位置组。 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>列出邻近放置组

你可以使用[az ppg list](/cli/azure/ppg#az-ppg-list)列出所有邻近位置组。

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>创建 VM

使用[新的 az VM](/cli/azure/vm#az-vm-create)在邻近位置组中创建 VM。

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

可以使用[az ppg show](/cli/azure/ppg#az-ppg-show)在邻近布局组中查看 VM。

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>可用性集
你还可以在邻近布局组中创建可用性集。 使用 az vm `--ppg`可用性的相同参数[-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create)创建可用性集，可用性集中的所有 vm 也将在同一邻近布局组中创建。

## <a name="scale-sets"></a>规模集

你还可以在邻近布局组中创建规模集。 使用`--ppg` [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)的相同参数创建规模集，所有实例都将在相同的邻近布局组中创建。

## <a name="next-steps"></a>后续步骤

了解有关邻近位置组的[Azure CLI](/cli/azure/ppg)命令的详细信息。
