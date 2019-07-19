---
title: 使用适用于 Linux Vm 的邻近位置组预览 |Microsoft Docs
description: 了解如何在 Azure 中创建和使用适用于 Linux 虚拟机的邻近组。
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 0d44e38343d6f7113b296b57353080e2de068bb6
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278282"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>预览版：使用 Azure CLI 将 Vm 部署到邻近位置组

若要尽可能降低 Vm, 请尽可能降低延迟, 然后将它们部署到[邻近的放置组](co-location.md#preview-proximity-placement-groups)。

邻近性放置组是一种逻辑分组, 用于确保 Azure 计算资源的物理位置彼此接近。 邻近性放置组适用于要求低延迟的工作负荷。

> [!IMPORTANT]
> 邻近性放置组目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 预览期间, 不会在这些区域中使用邻近位置组:**日本东部**、**澳大利亚东部**和**印度中部**。

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

## <a name="list-proximity-placement-groups"></a>列出邻近位置组

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

## <a name="availability-sets"></a>可用性集
你还可以在邻近布局组中创建可用性集。 使用 az vm `--ppg`可用性的相同参数[-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create)创建可用性集, 可用性集中的所有 vm 也将在同一邻近布局组中创建。

## <a name="scale-sets"></a>规模集

你还可以在邻近布局组中创建规模集。 使用`--ppg` [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)的相同参数创建规模集, 所有实例都将在相同的邻近布局组中创建。

## <a name="next-steps"></a>后续步骤

了解有关邻近位置组的[Azure CLI](/cli/azure/ppg)命令的详细信息。
