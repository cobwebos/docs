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
ms.date: 05/22/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 927eb9f4f40759c0a79daa6dd3e91e5ab0853520
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-use-availability-sets"></a>如何使用可用性集


本教程介绍如何使用称作“可用性集”的功能提高 Azure 上虚拟机解决方案的可用性和可靠性。 可用性集可确保在 Azure 上部署的 VM 能够跨多个隔离的硬件群集分布。 这样，就可以确保当 Azure 中发生硬件或软件故障时，只有一部分 VM 会受到影响，整体解决方案仍可供其客户使用和操作。

本教程将介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="availability-set-overview"></a>可用性集概述

可用性集是一种逻辑分组功能，在 Azure 中使用它可以确保将 VM 资源部署在 Azure 数据中心后，这些资源相互隔离。 Azure 确保可用性集中部署的 VM 能够跨多个物理服务器、计算机架、存储单元和网络交换机运行。 这样就可以确保当出现硬件或 Azure 软件故障时，只有一部分 VM 会受到影响，整体应用程序仍会保持运行，可供客户使用。 如果想要构建可靠的云解决方案，可用性集是要利用的一项关键功能。

假设某个基于 VM 的典型解决方案包含 4 个前端 Web 服务器，以及 2 个托管数据库的后端 VM。 在 Azure 中，你想要在部署 VM 之前先定义两个可用性集：一个可用性集用于“Web”层，另一个可用性集用于“数据库”层。 然后，在创建新的 VM 时，可在 az vm create 命令中指定可用性集作为参数，Azure 可自动确保在可用性集中创建的 VM 在多个物理硬件资源之间保持隔离。 这意味着，如果运行某个 Web 服务器或数据库服务器的物理硬件有问题，你可以确信 Web 服务器和数据库 VM 的其他实例会保持正常运行，因为它们位于不同的硬件上。

若要在 Azure 中部署可靠的基于 VM 的解决方案时，始终应该使用可用性集。


## <a name="create-an-availability-set"></a>创建可用性集

可使用 [az vm availability-set create](/cli/azure/availability-set#create) 创建可用性集。 在本示例中，将 myResourceGroupAvailability 资源组中名为 myAvailabilitySet 的可用性集的更新域数和容错域数均设置为 2。

创建资源组。

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

使用可用性集可跨“容错域”和“更新域”隔离资源。 **容错域**代表服务器、网络和存储资源的隔离集合。 前面的示例指出我们想要在部署 VM 时，将可用性集至少分布在两个容错域之间。 此外，还指出要将可用性集分布在两个**更新域**之间。  两个更新域确保当 Azure 执行软件更新时，VM 资源可以隔离，防止 VM 下面运行的所有软件同时更新。

## <a name="create-vms-inside-an-availability-set"></a>在可用性集内创建 VM

必须在可用性集中创建 VM，确保它们正确地分布在硬件中。 创建后，无法将现有 VM 添加到可用性集中。 

使用 [az vm create](/cli/azure/vm#create) 创建 VM 时，利用 `--availability-set` 参数指定可用性集，以指定该可用性集的名称。

```azurecli-interactive 
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

现在，我们已在新建的可用性集中创建了两个虚拟机。 由于它们在同一可用性集中，Azure 将确保 VM 及其所有资源（包括数据磁盘）分布在隔离的物理硬件上。 这种分布方式有助于确保提高整体 VM 解决方案的可用性。

添加 VM 时可能会遇到的一个问题是特定 VM 的大小不再适用于可用性集。 如果添加 VM 时容量不足，同时又要遵守可用性集强制实施的隔离规则，则可能会发生此问题。 可以使用 `--availability-set list-sizes` 参数来确定哪些 VM 大小适用于现有的可用性集。

## <a name="check-for-available-vm-sizes"></a>检查可用的 VM 大小 

稍后可向可用性集添加更多 VM，但需了解在硬件上可用的 VM 大小。 使用 [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) 列出可用性集的硬件群集上所有可用的大小。

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何执行以下操作：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小

请转到下一教程，了解虚拟机规模集。

> [!div class="nextstepaction"]
> [创建 VM 规模集](tutorial-create-vmss.md)


