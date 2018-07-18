---
title: 创建具有静态公共 IP 地址的 VM - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure 命令行接口 (CLI) 创建具有静态公共 IP 地址的 VM。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd44971162a79e53b731c5c89316f14e8bb0a1a6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651953"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>使用 Azure CLI 创建具有静态公共 IP 地址的 VM

> [!div class="op_single_selector"]
> * [Azure 门户](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell（经典）](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 本文介绍如何使用 Resource Manager 部署模型。Microsoft 建议对大多数新的部署使用该模型，而不是经典部署模型。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>创建 VM

在以下步骤中，"" 中的变量值使用本方案的设置创建资源。 根据需要更改环境值。

1. 安装 [Azure CLI 2.0](/cli/azure/install-az-cli2)（如果尚未安装）。
2. 通过完成[为 Linux VM 创建 SSH 公钥和私钥对](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步骤创建适用于 Linux VM 的 SSH 公钥和私钥对。
3. 从命令行界面，使用命令 `az login` 登录。
4. 通过在 Linux 或 Mac 计算机上执行以下脚本创建 VM。 Azure 公共 IP 地址、虚拟网络、网络接口和 VM 资源必须都存在于同一位置。 虽然资源不必都存在于同一资源组中，但是在以下脚本中资源都存在于同一资源组中。

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

除了创建 VM，该脚本还创建：
- 创建单个高级托管磁盘（默认情况下），但对于可以创建的磁盘类型，可以有其他选择。 有关详细信息，请阅读[使用 Azure CLI 2.0 创建 Linux VM](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文。
- 虚拟网络、子网、NIC 和公共 IP 地址资源。 或者，可以使用*现有*虚拟网络、子网、NIC 或公共 IP 地址资源。 若要了解如何使用现有网络资源，而不是创建其他资源，请输入 `az vm create -h`。

## <a name = "validate"></a>验证 VM 创建和公共 IP 地址

1. 输入命令 `az resource list --resouce-group IaaSStory --output table`，查看由脚本创建的资源的列表。 返回的输出中应该有五个资源：网络接口、磁盘、公共 IP 地址、虚拟网络和虚拟机。
2. 输入命令 `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`。 在返回的输出中，记下 **IpAddress** 的值，并且 **PublicIpAllocationMethod** 的值是 *Static*。
3. 在执行以下命令前，请删除 <>，将 Username 替换为用于脚本中 Username 变量的名称，并将 ipAddress 替换为上一步中的 ipAddress。 输入以下命令以连接到 VM：`ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`。 

## <a name= "clean-up"></a>删除 VM 和关联的资源

如果生产环境中不会使用此练习中创建的资源，建议将其删除。 VM、公共 IP 地址和磁盘资源在预配后将产生费用。 若要删除此练习中创建的资源，请完成以下步骤：

1. 若要查看资源组中的资源，请运行 `az resource list --resource-group IaaSStory` 命令。
2. 请确认在资源组中除了本文中脚本创建的资源外没有其他资源。 
3. 若要删除本练习中创建的所有资源，请运行 `az group delete -n IaaSStory` 命令。 该命令将删除资源组及其包含的所有资源。
 
## <a name="set-ip-addresses-within-the-operating-system"></a>在操作系统中设置 IP 地址

切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。 我们建议，除非有必要（例如，[为 Windows VM 分配多个 IP 地址](virtual-network-multiple-ip-addresses-cli.md)时），否则不要以静态方式在 VM 的操作系统中分配已分配给 Azure 虚拟机的专用 IP。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址是同一地址，否则可能会丢失与虚拟机的连接。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。

## <a name="next-steps"></a>后续步骤

任何网络流量都可流入和流出本文中创建的 VM。 可以在网络安全组中定义入站和出站安全规则，以限制可以流入和流出网络接口和/或子网的流量。 若要深入了解网络安全组，请参阅[网络安全组概述](security-overview.md)。