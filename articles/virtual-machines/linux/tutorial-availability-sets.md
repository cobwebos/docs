---
title: "Azure 中 Linux VM 的可用性集教程 | Microsoft Docs"
description: "了解 Azure 中 Linux VM 的可用性集。"
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: d082b37a2e070136178259c54ada8dc141f81e13
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>如何使用可用性集

本教程介绍如何通过将虚拟机 (VM) 放入名为可用性集的逻辑分组中来提高虚拟机可用性。 在可用性集中创建 VM 时，Azure 平台会跨底层基础结构分发 VM。 如果平台上存在硬件故障或计划维护，则使用可用性集可确保至少有一个 VM 保持运行。

可使用最新版 [Azure CLI 2.0](/cli/azure/install-azure-cli) 完成本教程中的步骤。

## <a name="availability-set-overview"></a>可用性集概述

可在基础 Azure 数据中心跨硬件的逻辑分组创建虚拟机。 创建两个或多个 VM 时，跨硬件分发计算和存储资源，例如服务器、网络交换机和存储。 如果硬件组件正在进行维护，此分配将维持你的应用的可用性。 使用可用性集可定义此逻辑分组。

可用性集为 VM 提供高可用性。 此外，应确保应用程序可容忍故障或维护事件。

## <a name="create-an-availability-set"></a>创建可用性集

可使用 [az vm availability-set create](/cli/azure/availability-set#create) 创建可用性集。 在本示例中，将 myResourceGroupAvailability 资源组中名为 myAvailabilitySet 的可用性集的更新域数和容错域数均设置为 2。

```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## <a name="create-vms-inside-an-availability-set"></a>在可用性集内创建 VM

需要在可用性集内创建 VM，以确保其正确地分布在硬件中。 创建后，无法将现有 VM 添加到可用性集中。 

同一位置的硬件分为多个更新域和容错域。 更新域是一组可同时重启的 VM 和基础物理硬件。 同一个容错域内的 VM 共享公用存储，以及公用电源和网络交换机。 

使用 [az vm create](/cli/azure/vm#create) 创建 VM 时，利用 `--availability-set` 参数指定可用性集，以指定该可用性集的名称。

```azurecli
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

我们现在有 2 个分布在基础硬件上的虚拟机。 

## <a name="check-for-available-vm-sizes"></a>检查可用的 VM 大小 

稍后可向可用性集添加更多 VM，但需了解在硬件上可用的 VM 大小。 使用 [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) 列出可用性集的硬件群集上所有可用的大小。

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何使用可用性集。 请转到下一教程，了解虚拟机规模集。

[创建 VM 规模集](tutorial-create-vmss.md)


