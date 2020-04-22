---
title: 使用邻近放置组
description: 了解如何在 Azure 中创建和使用虚拟机的邻近放置组。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: f89b28e7a3c29e45efa2796788e27325c01d7098
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759251"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>使用 Azure CLI 将 VM 部署到邻近放置组

要尽可能接近 VM，实现尽可能低的延迟，应将它们部署在[接近放置组中](co-location.md#proximity-placement-groups)。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。


## <a name="create-the-proximity-placement-group"></a>创建邻近放置组
使用[`az ppg create`](/cli/azure/ppg#az-ppg-create)创建接近放置组。 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>列出邻近放置组

您可以使用[az ppg 列表](/cli/azure/ppg#az-ppg-list)列出所有邻近放置组。

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>创建 VM

使用新的 az vm 在接近放置组中创建[VM。](/cli/azure/vm#az-vm-create)

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

您可以使用[az ppg 显示](/cli/azure/ppg#az-ppg-show)在接近放置组中看到 VM。

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>可用性集
您还可以在邻近放置组中创建可用性集。 使用同一`--ppg`参数创建[az vm 可用性集](/cli/azure/vm/availability-set#az-vm-availability-set-create)以创建可用性集，并且可用性集中的所有 VM 也将在同一接近放置组中创建。

## <a name="scale-sets"></a>规模集

您还可以在邻近放置组中创建比例集。 使用同一`--ppg`参数创建[az vmss](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)以创建比例集，并将在同一邻近放置组中创建所有实例。

## <a name="next-steps"></a>后续步骤

详细了解接近放置组的[Azure CLI](/cli/azure/ppg)命令。
