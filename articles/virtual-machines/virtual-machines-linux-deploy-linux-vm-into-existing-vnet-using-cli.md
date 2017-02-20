---
title: "通过 Azure CLI 2.0（预览版）将 Linux VM 部署到现有网络 |Microsoft Docs"
description: "了解如何通过 Azure CLI 2.0（预览版）将 Linux 虚拟机部署到现有虚拟网络"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63485f0c9e151db22f23d291e2a4425dd01fb7ee
ms.openlocfilehash: b22ac95ee11fe059d36a9416434a14814da1ee7d


---

# <a name="deploy-a-linux-vm-into-an-existing-virtual-network-using-the-azure-cli-20-preview"></a>通过 Azure CLI 2.0（预览版）将 Linux VM 部署到现有虚拟网络

本文介绍了如何通过 Azure CLI 2.0（预览版）将虚拟机 (VM) 部署到现有虚拟网络。 要求如下：

- [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)
- [SSH 公钥和私钥文件](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于经典部署模型和资源管理部署模型的 CLI
- [Azure CLI 2.0（预览版）](#quick-commands)- 适用于资源管理部署模型的下一代 CLI（本文）


## <a name="quick-commands"></a>快速命令
如果需要快速完成任务，以下部分详细介绍所需的命令。 本文档的余下部分（[从此处开始](#detailed-walkthrough)）提供了每个步骤的更详细信息和上下文。

若要创建此自定义环境，需要安装最新的 [Azure CLI 2.0（预览版）](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 `myResourceGroup`、`myVnet` 和 `myVM`。

**先决条件：**Azure 资源组、虚拟网络和子网、带 SSH 入站的网络安全组以及虚拟网络接口卡。

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>将 VM 部署到虚拟网络基础结构

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup
```

## <a name="detailed-walkthrough"></a>详细演练

建议选择静态的、长期存在的且部署频率极低的资源作为 Azure 资产，例如虚拟网络和网络安全组。 部署虚拟网络后，新部署可以重复使用它，而不会对基础结构产生任何负面影响。 可以将虚拟网络想像为传统硬件网络交换机，用户不需要为全新的硬件交换机配置每个部署。 正确配置虚拟网络后，在该虚拟网络的整个生命周期中，我们可以继续反复将新 VM 部署到该虚拟网络，只需做很少的更改（如果有）。

若要创建此自定义环境，需要安装最新的 [Azure CLI 2.0（预览版）](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 `myResourceGroup`、`myVnet` 和 `myVM`。

## <a name="create-the-resource-group"></a>创建资源组

首先，我们将创建 Azure 资源组，以便组织在本演练中创建的所有内容。 有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 使用 [az group create](/cli/azure/group#create) 创建资源组。 以下示例在 `westus` 位置创建名为 `myResourceGroup` 的资源组：

```azurecli
az group create \
    --name myResourceGroup \
    --location westus
```

## <a name="create-the-virtual-network"></a>创建虚拟网络

现在创建一个 Azure 虚拟网络，以便在其中启动 VM。 有关 Azure 虚拟网络的详细信息，请参阅[使用 Azure CLI 创建虚拟网络](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 使用 [az network vnet create](/cli/azure/network/vnet#create) 创建虚拟网络。 以下示例创建一个名为 `myVnet` 的虚拟网络和名为 `mySubnet` 的子网：

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location westus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>创建网络安全组

Azure 网络安全组相当于网络层防火墙。 有关网络安全组的详细信息，请参阅[如何在 Azure CLI 中创建网络安全组](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 使用 [az 网络 nsg 创建](/cli/azure/network/nsg#create)创建网络安全组。 以下示例创建名为 `myNetworkSecurityGroup` 的网络安全组：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>添加入站 SSH 允许规则

Linux VM 需要从 Internet 访问，因此需要允许通过网络将入站端口 22 流量传递到 Linux VM 上的端口 22 的规则。 使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 为网络安全组添加入站规则。 以下示例创建一个名为 `myNetworkSecurityGroupRuleSSH` 的规则：

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>将子网附加到网络安全组

可以将网络安全组应用于子网或特定虚拟网络接口。 现在将网络安全组附加到子网。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) 将子网附加到网络安全组：

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>将虚拟网络接口卡添加到子网

虚拟网络接口卡 (VNic) 可连接到不同的 VM 供用户重复使用，因此很重要。 此重复使用特性使用户可以将 VNic 作为静态资源保存，而 VM 可以保存为临时资源。 使用 [az network nic create](/cli/azure/network/nic#create) 创建 VNic 并将其与子网关联。 以下示例创建一个名为 `myNic` 的 VNic：

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>将 VM 部署到虚拟网络基础结构

我们现在已有一个虚拟网络、一个子网，以及一个充当防火墙的网络安全组，该网络安全组可以通过阻止所有入站流量（用于 SSH 的端口 22 除外）来保护子网。 现在可以将 VM 部署到这个现有的网络基础结构内。

使用 [az vm create](/cli/azure/vm#create) 创建 VM。 若要详细了解与 Azure CLI 2.0（预览版）结合使用以部署完整的 VM 的标志，请参阅[使用 Azure CLI 创建完整的 Linux 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup
```

使用 CLI 标志调用现有资源是为了指示 Azure 将 VM 部署到现有网络中。 重述一遍，部署虚拟网络和子网以后，即可将其作为静态资源或永久资源留在 Azure 区域。 在此示例中，我们并未为 VNic 创建并分配公共 IP 地址，因此，无法通过 Internet 公开访问该 VM。 有关详细信息，请参阅[使用 Azure CLI 创建具有静态公共 IP 的 VM](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="next-steps"></a>后续步骤
若要深入了解在 Azure 中创建虚拟机的各种方法，请参阅以下资源：

* [使用 Azure Resource Manager 模板创建特定部署](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用模板在 Azure 上创建 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO1-->


