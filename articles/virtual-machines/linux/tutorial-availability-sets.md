---
title: 教程 - Azure 中 Linux VM 的高可用性 | Microsoft Docs
description: 本教程介绍了如何使用 Azure CLI 2.0 在可用性集中部署高度可用的虚拟机
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: dc6fba89571515d0d2d7ed3ecc35c3065405056b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188423"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli-20"></a>教程：使用 Azure CLI 2.0 创建和部署高度可用的虚拟机

本教程介绍如何使用称作“可用性集”的功能提高 Azure 上虚拟机解决方案的可用性和可靠性。 可用性集可确保在 Azure 上部署的 VM 能够跨多个隔离的硬件群集分布。 这样，就可以确保当 Azure 中发生硬件或软件故障时，只有一部分 VM 受到影响，整体解决方案仍可使用和操作。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="availability-set-overview"></a>可用性集概述

可用性集是一种逻辑分组功能，在 Azure 中使用它可以确保将 VM 资源部署在 Azure 数据中心后，这些资源相互隔离。 Azure 确保可用性集中部署的 VM 能够跨多个物理服务器、计算机架、存储单元和网络交换机运行。 如果出现硬件或 Azure 软件故障，只有一部分 VM 会受到影响，整体应用程序仍会保持运行，可供客户使用。 如果想要构建可靠的云解决方案，可用性集是一项关键功能。

假设某个基于 VM 的典型解决方案包含四个前端 Web 服务器，以及两个托管数据库的后端 VM。 在 Azure 中，若想在部署 VM 之前先定义两个可用性集：一个可用性集用于“Web”层级，另一个可用性集用于“数据库”层级。 创建新的 VM 时，可在 az vm create 命令中指定可用性集作为参数，Azure 会自动确保在可用性集中创建的 VM 在多个物理硬件资源之间保持独立。 如果运行某个 Web 服务器或数据库服务器 VM 的物理硬件有问题，可以确信 Web 服务器和数据库 VM 的其他实例会保持运行，因为它们位于不同的硬件上。

在 Azure 中部署基于 VM 的可靠解决方案时，使用可用性集。


## <a name="create-an-availability-set"></a>创建可用性集

可使用 [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create) 创建可用性集。 在本示例中，*myResourceGroupAvailability* 资源组中名为 *myAvailabilitySet* 的可用性集的更新域数和容错域数均设置为 *2*。

首先，使用 [az group create](/cli/azure/group#az-group-create) 创建资源组，然后创建可用性集：

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

使用可用性集可跨容错域和更新域隔离资源。 **容错域**代表服务器、网络和存储资源的隔离集合。 在前面的示例中，在部署 VM 时，可用性集至少分布在两个容错域中。 可用性集还分布在两个**更新域**中。 两个更新域确保当 Azure 执行软件更新时，VM 资源是隔离的，防止在 VM 上运行的所有软件同时更新。


## <a name="create-vms-inside-an-availability-set"></a>在可用性集内创建 VM

必须在可用性集中创建 VM，确保它们正确地分布在硬件中。 在创建可用性集后，无法将现有 VM 添加到可用性集中。

通过 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM 时，请使用 `--availability-set` 参数指定可用性集的名称。

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done
```

现在，可用性集内有两台虚拟机。 由于它们在同一可用性集中，Azure 会确保 VM 及其所有资源（包括数据磁盘）分布在隔离的物理硬件上。 这种分布方式有助于确保提高整体 VM 解决方案的可用性。

可以在门户中转到“资源组”> myResourceGroupAvailability > myAvailabilitySet 来查看可用性集分布。 VM 分布在两个容错和更新域中，如以下示例所示：

![门户中的可用性集](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>检查可用的 VM 大小

以后可以向可用性集添加更多 VM（VM 大小可在硬件上获得）。 使用 [az vm availability-set list-sizes](/cli/azure/availability-set#az_availability_set_list_sizes) 列出可用性集的硬件群集上所有可用的大小：

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小

请转到下一教程，了解虚拟机规模集。

> [!div class="nextstepaction"]
> [创建虚拟机规模集](tutorial-create-vmss.md)