---
title: "Azure 虚拟网络和 Linux 虚拟机 | Microsoft Docs"
description: "教程 - 使用 Azure CLI 管理 Azure 虚拟网络和 Linux 虚拟机"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: de7e77b7d4c26b08e73036b8da67489823100f4c
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# 使用 Azure CLI 管理 Azure 虚拟网络和 Linux 虚拟机
<a id="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli" class="xliff"></a>

Azure 虚拟机使用 Azure 网络进行内部和外部网络通信。 本教程将指导读者部署两个虚拟机，并为这些 VM 配置 Azure 网络。 本教程中的示例假设 VM 将要托管包含数据库后端的 Web 应用程序，但本教程并未介绍如何部署应用程序。 本教程将介绍如何执行下列操作：

> [!div class="checklist"]
> * 部署虚拟网络
> * 在虚拟网络中创建子网
> * 将虚拟机附加到子网
> * 管理虚拟机公共 IP 地址
> * 保护传入的 Internet 流量
> * 保护 VM 到 VM 的流量


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## VM 网络概述
<a id="vm-networking-overview" class="xliff"></a>

Azure 虚拟网络在虚拟机、Internet 与其他 Azure 服务（例如 Azure SQL 数据库）之间实现安全网络连接。 虚拟网络分解为称作“子网”的逻辑段。 子网用于控制网络流，并充当安全边界。 部署 VM 时，该 VM 通常包含一个附加到子网的虚拟网络接口。

## 部署虚拟网络
<a id="deploy-virtual-network" class="xliff"></a>

本教程将创建包含两个子网的单个虚拟网络。 一个前端子网用于托管 Web 应用程序，一个后端子网用于托管数据库服务器。

在创建虚拟网络之前，请先使用 [az group create](/cli/azure/group#create) 创建一个资源组。 以下示例在 eastus 位置创建名为 *myRGNetwork* 的资源组。

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### 创建虚拟网络
<a id="create-virtual-network" class="xliff"></a>

使用 [az network vnet create](/cli/azure/network/vnet#create) 命令创建虚拟网络。 在本示例中，为名为 *mvVnet* 的网络分配了前缀为 *10.0.0.0/16* 的地址。 另外，还创建了名为 *mySubnetFrontEnd*、前缀为 *10.0.1.0/24* 的子网。 本教程稍后会将前端 VM 连接到此子网。 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnetFrontEnd \
  --subnet-prefix 10.0.1.0/24
```

### 创建子网
<a id="create-subnet" class="xliff"></a>

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#create) 命令将新子网添加到虚拟网络。 在本示例中，为名为 *mySubnetBackEnd* 的子网分配了前缀为 *10.0.2.0/24* 的地址。 此子网用于所有后端服务。

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --address-prefix 10.0.2.0/24
```

此时，已创建一个网络并将其分段为两个子网，其中一个子网用于前端服务，另一个用于后端服务。 下一部分将创建虚拟机并将其连接到这些子网。

## 了解公共 IP 地址
<a id="understand-public-ip-address" class="xliff"></a>

使用公共 IP 地址可在 Internet 上访问 Azure 资源。 本教程的此部分将创建一个 VM 用于演示如何使用公共 IP 地址。

### 分配方法
<a id="allocation-method" class="xliff"></a>

可以分配动态或静态公共 IP 地址。 默认情况下，将动态分配公共 IP 地址。 解除分配 VM 时，将释放动态 IP 地址。 在执行涉及到 VM 解除分配的任何操作期间，此行为会导致 IP 地址发生更改。

可将分配方法设置为静态，这可确保分配给 VM 的 IP 地址保持不变，即使该 VM 处于解除分配状态。 使用静态分配的 IP 地址时，无法指定 IP 地址本身。 该地址是从可用地址池中分配的。

### 动态分配
<a id="dynamic-allocation" class="xliff"></a>

使用 [az vm create](/cli/azure/vm#create) 命令创建 VM 时，默认的公共 IP 地址分配方法为动态。 在以下示例中，已创建一个具有动态 IP 地址的 VM。 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontEndVM \
  --vnet-name myVnet \
  --subnet mySubnetFrontEnd \
  --nsg myNSGFrontEnd \
  --public-ip-address myFrontEndIP \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### 静态分配
<a id="static-allocation" class="xliff"></a>

使用 [az vm create](/cli/azure/vm#create) 命令创建虚拟机时，包含 `--public-ip-address-allocation static` 参数可以分配静态公共 IP 地址。 本教程不会演示此操作，但是，下一部分介绍了如何将动态分配的 IP 地址更改为静态分配的地址。 

### 更改分配方法
<a id="change-allocation-method" class="xliff"></a>

可以使用 [az network public-ip update](/cli/azure/network/public-ip#update) 命令更改 IP 地址分配方法。 在本示例中，前端 VM 的 IP 地址分配方法已更改为静态。

首先，请解除分配 VM。

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontEndVM
```

使用 [az network public-ip update](/azure/network/public-ip#update) 命令更新分配方法。 在本例中，`--allocaion-metod` 将设置为 *static*。

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myFrontEndIP --allocation-method static
```

启动 VM。

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontEndVM --no-wait
```

### 无公共 IP 地址
<a id="no-public-ip-address" class="xliff"></a>

通常，不需要通过 Internet 访问 VM。 若要创建一个不带公共 IP 地址的 VM，请使用 `--public-ip-address ""` 参数和一组空双引号。 本教程稍后将演示此配置

## 保护网络流量的安全
<a id="secure-network-traffic" class="xliff"></a>

网络安全组 (NSG) 包含一系列安全规则，这些规则可以允许或拒绝流向连接到 Azure 虚拟网络 (VNet) 的资源的网络流量。 NSG 可以关联到子网或单个网络接口。 当 NSG 与网络接口关联时，NSG 只会应用到关联的 VM。 将 NSG 关联到子网时，规则适用于连接到该子网的所有资源。 

### 网络安全组规则
<a id="network-security-group-rules" class="xliff"></a>

NSG 规则定义要允许或拒绝哪些网络端口上的流量。 这些规则可以包括源和目标 IP 地址范围，以便控制特定系统或子网之间的流量。 NSG 规则还包括优先级（介于 1 和 4096 之间）。 将按优先级顺序来评估规则。 优先级为 100 的规则将在优先级为 200 的规则之前评估。

所有 NSG 都包含一组默认规则。 默认规则无法删除，但由于给它们分配的优先级最低，可以用创建的规则来重写它们。

- **虚拟网络** - 从方向上来说，在虚拟网络中发起和结束的通信可以是入站通信，也可以是出站通信。
- **Internet** - 允许出站通信，但阻止入站通信。
- **负载均衡器** - 允许 Azure 的负载均衡器探测 VM 和角色实例的运行状况。 如果不使用负载均衡集，则可替代此规则。

### 创建网络安全组
<a id="create-network-security-groups" class="xliff"></a>

可以在创建 VM 时使用 [az vm create](/cli/azure/vm#create) 命令创建网络安全组。 执行此操作时，会将 NSG 与 VM 网络接口相关联，并自动创建一个 NSG 规则来允许任何源发到 *22* 端口上的流量。 在本教程的前面部分中，已在前端 VM 上自动创建了前端 NSG。 也会为端口 22 自动创建 NSG 规则。 

在某些情况下，预先创建 NSG 可能会有帮助，例如，在不应创建默认 SSH 规则时，或者应该将 NSG 附加到子网时。 

使用 [az network nsg create](/cli/azure/network/nsg#create) 命令创建网络安全组。

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myNSGBackEnd
```

NSG 不会关联到网络接口，而是关联到子网。 在此配置中，附加到子网的任何 VM 将继承 NSG 规则。

使用新 NSG 更新名为 *mySubnetBackEnd* 的现有子网。

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --network-security-group myNSGBackEnd
```

现在，请创建附加到 *mySubnetBackEnd* 的虚拟机。 请注意，`--nsg` 参数的值为空双引号。 不需要在 VM 中创建 NSG。 VM 将附加到后端子网，该子网受预先创建的后端 NSG 的保护。 此 NSG 将应用到 VM。 另请注意，此处的 `--public-ip-address` 参数的值为空双引号。 此配置将创建一个不带公共 IP 地址的 VM。 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackEndVM \
  --vnet-name myVnet \
  --subnet mySubnetBackEnd \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### 保护传入的流量
<a id="secure-incoming-traffic" class="xliff"></a>

创建前端 VM 时，会创建一个 NSG 规则来允许端口 22 上的传入流量。 此规则允许与 VM 建立 SSH 连接。 对于本示例，也应该允许端口 *80* 上的流量。 此配置允许在 VM 上访问某个 Web 应用程序。

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 命令为端口 *80* 创建规则。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGFrontEnd \
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

现在，只能在端口 *22* 和端口 *80* 上访问前端 VM。 其他所有传入流量将在网络安全组中被阻止。 可视化 NSG 规则配置可能很有帮助。 使用 [az network rule list](/cli/azure/network/nsg/rule#list) 命令返回 NSG 规则配置。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGFrontEnd --output table
```

输出：

```azurecli-interactive 
Access    DestinationAddressPrefix      DestinationPortRange  Direction    Name                 Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -----------------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                                               22  Inbound      default-allow-ssh        1000  Tcp         Succeeded            myRGNetwork      *                      *
Allow     *                                               80  Inbound      http                      200  Tcp         Succeeded            myRGNetwork      *                      *
```

### 保护 VM 到 VM 的流量
<a id="secure-vm-to-vm-traffic" class="xliff"></a>

也可以在 VM 之间应用网络安全组规则。 对于本示例，前端 VM 需要通过端口 *22* 和 *3306* 与后端 VM 端口通信。 此配置允许从前端 VM 建立 SSH 连接，并允许前端 VM 上的应用程序与后端 MySQL 数据库通信。 前端与后端虚拟机之间的其他所有流量应被阻止。

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 命令为端口 22 创建规则。 请注意，`--source-address-prefix` 参数指定了值 *10.0.1.0/24*。 此配置确保只允许来自前端子网的流量通过 NSG。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
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
  --nsg-name myNSGBackEnd \
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
  --nsg-name myNSGBackEnd \
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

现在，只能通过前端子网在端口 *22* 和端口 *3306* 上访问后端 VM。 其他所有传入流量将在网络安全组中被阻止。 可视化 NSG 规则配置可能很有帮助。 使用 [az network rule list](/cli/azure/network/nsg/rule#list) 命令返回 NSG 规则配置。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGBackEnd --output table
```

输出：

```azurecli-interactive 
Access    DestinationAddressPrefix    DestinationPortRange    Direction    Name       Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                           22                      Inbound      SSH             100  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Allow     *                           3306                    Inbound      MySQL           200  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Deny      *                           *                       Inbound      denyAll         300  Tcp         Succeeded            myRGNetwork      *                      *
```

## 后续步骤
<a id="next-steps" class="xliff"></a>

本教程介绍了如何创建和保护与虚拟机相关的 Azure 网络。 你已了解如何：

> [!div class="checklist"]
> * 部署虚拟网络
> * 在虚拟网络中创建子网
> * 将虚拟机附加到子网
> * 管理虚拟机公共 IP 地址
> * 保护传入的 Internet 流量
> * 保护 VM 到 VM 的流量

请继续学习下一教程，了解如何使用 Azure 备份保护虚拟机上的数据。 

> [!div class="nextstepaction"]
> [在 Azure 中备份 Linux 虚拟机](./tutorial-backup-vms.md)

