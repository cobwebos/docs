---
title: 教程 - 为 Linux VM 创建和管理 Azure 虚拟网络 | Microsoft Docs
description: 本教程介绍如何使用 Azure CLI 2.0 为 Linux 虚拟机创建和管理 Azure 虚拟网络
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 306d33dd5b5910e990caf80dae4c37fee020f7a1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli-20"></a>教程：使用 Azure CLI 2.0 为 Linux 虚拟机创建和管理 Azure 虚拟网络

Azure 虚拟机使用 Azure 网络进行内部和外部网络通信。 本教程会指导读者部署两个虚拟机，并为这些 VM 配置 Azure 网络。 本教程中的示例假设 VM 将要托管包含数据库后端的 Web 应用程序，但本教程并未介绍如何部署应用程序。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建虚拟网络和子网
> * 创建公共 IP 地址
> * 创建前端 VM
> * 保护网络流量的安全
> * 创建后端 VM

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="vm-networking-overview"></a>VM 网络概述

Azure 虚拟网络在虚拟机、Internet 与其他 Azure 服务（例如 Azure SQL 数据库）之间实现安全网络连接。 虚拟网络分解为称作“子网”的逻辑段。 子网用于控制网络流，并充当安全边界。 部署 VM 时，该 VM 通常包含一个附加到子网的虚拟网络接口。

完成本教程后，会创建以下虚拟网络资源：

![包含两个子网的虚拟网络](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM 彼此进行通信以及与 Internet 进行通信时使用的虚拟网络。
- *myFrontendSubnet* - *myVNet* 中供前端资源使用的子网。
- *myPublicIPAddress* - 用于从 Internet 访问 *myFrontendVM* 的公共 IP 地址。
- *myFrontentNic* - *myFrontendVM* 用来与 *myBackendVM* 进行通信的网络接口。
- *myFrontendVM* - 用于在 Internet 和 *myBackendVM* 之间进行通信的 VM。
- *myBackendNSG* - 控制 *myFrontendVM* 和 *myBackendVM* 之间的通信的网络安全组。
- *myBackendSubnet* - 与 *myBackendNSG* 关联且供后端资源使用的子网。
- *myBackendNic* - *myBackendVM* 用于与 *myFrontendVM* 进行通信的网络接口。
- *myBackendVM* - 使用端口 22 和 3306 与 *myFrontendVM* 进行通信的 VM。

## <a name="create-a-virtual-network-and-subnet"></a>创建虚拟网络和子网

本教程会创建包含两个子网的单个虚拟网络。 一个前端子网用于托管 Web 应用程序，一个后端子网用于托管数据库服务器。

在创建虚拟网络之前，请先使用 [az group create](/cli/azure/group#az_group_create) 创建一个资源组。 以下示例在 eastus 位置创建名为 *myRGNetwork* 的资源组。

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 命令创建虚拟网络。 在本例中，网络被命名为 *mvVNet* 并且为其分配了地址前缀 *10.0.0.0/16*。 另外，还将创建名为 *myFrontendSubnet*、前缀为 *10.0.1.0/24* 的子网。 本教程稍后会将前端 VM 连接到此子网。 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>创建子网

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 命令将新子网添加到虚拟网络。 在本例中，子网被命名为 *myBackendSubnet* 并且为其分配了地址前缀 *10.0.2.0/24*。 此子网用于所有后端服务。

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

此时，已创建一个网络并将其分段为两个子网，其中一个子网用于前端服务，另一个用于后端服务。 下一部分将创建虚拟机并将其连接到这些子网。

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用公共 IP 地址可在 Internet 上访问 Azure 资源。 公共 IP 地址的分配方法可以配置为动态或静态。 默认情况下，将动态分配公共 IP 地址。 解除分配 VM 时，将释放动态 IP 地址。 在执行涉及到 VM 解除分配的任何操作期间，此行为会导致 IP 地址发生更改。

可将分配方法设置为静态，这可确保分配给 VM 的 IP 地址保持不变，即使该 VM 处于解除分配状态也是如此。 使用静态分配的 IP 地址时，无法指定 IP 地址本身。 该地址是从可用地址池中分配的。

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建 VM 时，默认的公共 IP 地址分配方法为动态。 使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建虚拟机时，包含 `--public-ip-address-allocation static` 参数可以分配静态公共 IP 地址。 本教程不会演示此操作，但是，下一部分介绍了如何将动态分配的 IP 地址更改为静态分配的地址。 

### <a name="change-allocation-method"></a>更改分配方法

可以使用 [az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) 命令更改 IP 地址分配方法。 在本示例中，前端 VM 的 IP 地址分配方法已更改为静态。

首先，请解除分配 VM。

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

使用 [az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) 命令更新分配方法。 在本例中，`--allocation-method` 将设置为 *static*。

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

启动 VM。

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>无公共 IP 地址

通常，不需要通过 Internet 访问 VM。 若要创建一个不带公共 IP 地址的 VM，请使用 `--public-ip-address ""` 参数和一组空双引号。 本教程稍后将演示此配置。

## <a name="create-a-front-end-vm"></a>创建前端 VM

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建名为 *myFrontendVM* 且使用 *myPublicIPAddress* 的 VM。

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>保护网络流量的安全

网络安全组 (NSG) 包含一系列安全规则，这些规则可以允许或拒绝流向连接到 Azure 虚拟网络 (VNet) 的资源的网络流量。 NSG 可以关联到子网或单个网络接口。 当 NSG 与网络接口关联时，NSG 只会应用到关联的 VM。 将 NSG 关联到子网时，规则适用于连接到该子网的所有资源。 

### <a name="network-security-group-rules"></a>网络安全组规则

NSG 规则定义要允许或拒绝哪些网络端口上的流量。 这些规则可以包括源和目标 IP 地址范围，以便控制特定系统或子网之间的流量。 NSG 规则还包括优先级（介于 1 和 4096 之间）。 将按优先级顺序来评估规则。 优先级为 100 的规则会在优先级为 200 的规则之前评估。

所有 NSG 都包含一组默认规则。 默认规则无法删除，但由于给它们分配的优先级最低，可以用创建的规则来重写它们。

NSG 的默认规则包括：

- **虚拟网络** - 从方向上来说，在虚拟网络中发起和结束的通信可以是入站通信，也可以是出站通信。
- **Internet** - 允许出站通信，但阻止入站通信。
- **负载均衡器** - 允许 Azure 的负载均衡器探测 VM 和角色实例的运行状况。 如果不使用负载均衡集，则可替代此规则。

### <a name="create-network-security-groups"></a>创建网络安全组

可以在创建 VM 时使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建网络安全组。 执行此操作时，会将 NSG 与 VM 网络接口相关联，并自动创建一个 NSG 规则来允许任何源发到 *22* 端口上的流量。 在本教程的前面部分中，已在前端 VM 上自动创建了前端 NSG。 也会为端口 22 自动创建 NSG 规则。 

在某些情况下，预先创建 NSG 可能会有帮助，例如，在不应创建默认 SSH 规则时，或者应该将 NSG 附加到子网时。 

使用 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) 命令创建网络安全组。

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

NSG 不会关联到网络接口，而是关联到子网。 在此配置中，附加到子网的任何 VM 将继承 NSG 规则。

使用新 NSG 更新名为 *myBackendSubnet* 的现有子网。

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>保护传入的流量

创建前端 VM 时，会创建一个 NSG 规则来允许端口 22 上的传入流量。 此规则允许与 VM 建立 SSH 连接。 对于本示例，也应该允许端口 *80* 上的流量。 此配置允许在 VM 上访问某个 Web 应用程序。

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 命令为端口 *80* 创建规则。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

只能在端口 *22* 和端口 *80* 上访问前端 VM。 其他所有传入流量会在网络安全组中被阻止。 可视化 NSG 规则配置可能很有帮助。 使用 [az network rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) 命令返回 NSG 规则配置。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>保护 VM 到 VM 的流量

也可以在 VM 之间应用网络安全组规则。 对于本示例，前端 VM 需要通过端口 *22* 和 *3306* 与后端 VM 端口通信。 此配置允许从前端 VM 建立 SSH 连接，并允许前端 VM 上的应用程序与后端 MySQL 数据库通信。 前端与后端虚拟机之间的其他所有流量应被阻止。

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 命令为端口 22 创建规则。 请注意，`--source-address-prefix` 参数指定了值 *10.0.1.0/24*。 此配置确保只允许来自前端子网的流量通过 NSG。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

现在，请为端口 3306 上的 MySQL 流量添加规则。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

最后，由于 NSG 包含的默认规则允许同一 VNet 中 VM 之间的所有流量，因此，可为后端 NSG 创建一个规则来阻止所有流量。 此处请注意，为 `--priority` 指定了值 *300*，此优先级低于 NSG 和 MySQL 规则的优先级。 此配置可确保仍允许 SSH 和 MySQL 流量通过 NSG。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>创建后端 VM

现在，请创建附加到 *myBackendSubnet* 的虚拟机。 请注意，`--nsg` 参数的值为空双引号。 不需要在 VM 中创建 NSG。 VM 将附加到后端子网，该子网受预先创建的后端 NSG 的保护。 此 NSG 将应用到 VM。 另请注意，此处的 `--public-ip-address` 参数的值为空双引号。 此配置将创建一个不带公共 IP 地址的 VM。 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

只能通过前端子网在端口 *22* 和端口 *3306* 上访问后端 VM。 其他所有传入流量会在网络安全组中被阻止。 可视化 NSG 规则配置可能很有帮助。 使用 [az network rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) 命令返回 NSG 规则配置。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建和保护与虚拟机相关的 Azure 网络。 你已了解如何：

> [!div class="checklist"]
> * 创建虚拟网络和子网
> * 创建公共 IP 地址
> * 创建前端 VM
> * 保护网络流量的安全
> * 创建后端 VM

请继续学习下一教程，了解如何使用 Azure 备份保护虚拟机上的数据。 

> [!div class="nextstepaction"]
> [在 Azure 中备份 Linux 虚拟机](./tutorial-backup-vms.md)
