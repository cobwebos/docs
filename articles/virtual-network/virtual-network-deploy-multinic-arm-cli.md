---
title: "创建具有多个 NIC 的 VM - Azure CLI 2.0 | Microsoft 文档"
description: "了解如何使用 Azure CLI 2.0 创建具有多个 NIC 的 VM。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 4f5eaf5f6ba56709b69d97c1f646f71396fd031b
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 创建具有多个 NIC 的 VM

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../resource-manager-deployment-model.md)。  本文介绍如何使用 Resource Manager 部署模型。Microsoft 建议对大多数新的部署使用该模型，而不是[经典部署模型](virtual-network-deploy-multinic-classic-cli.md)。
>

## <a name="create"></a>创建 VM

可以使用 Azure CLI 2.0（本文）或 [Azure CLI 1.0](virtual-network-deploy-multinic-cli-nodejs.md) 完成此任务。 在以下步骤中，"" 中的变量值使用本方案的设置创建资源。 根据需要更改你的环境值。

1. 安装 [Azure CLI 2.0](/cli/azure/install-az-cli2)（如果尚未安装）。
2. 通过完成[为 Linux VM 创建 SSH 公钥和私钥对](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步骤创建适用于 Linux VM 的 SSH 公钥和私钥对。
3. 从命令行界面，使用命令 `az login` 登录。
4. 通过在 Linux 或 Mac 计算机上执行以下脚本创建 VM。 该脚本创建一个资源组、一个包含两个子网的虚拟网络 (VNet)、两个 NIC 和一个附加有两个 NIC 的 VM。 其中一个 NIC 连接到一个子网，并分配有一个静态公共 IP 地址和一个专用 IP 地址。 另一个 NIC 连接到其他子网，并分配有一个静态专用 IP 地址，未分配公共 IP 地址。 NIC、公共 IP 地址、虚拟网络和 VM 资源必须存在于同一位置和同一订阅中。 虽然资源不必都存在于同一资源组中，但是在以下脚本中资源都存在于同一资源组中。

```bash
#!/bin/sh

RgName="Multi-NIC-VM"
Location="westus"

# Create a resource group.
az group create \
--name $RgName \
--location $Location

# The address is assigned to the resource from a pool of IP adresses unique to each Azure region. 
# Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
# the ranges for each region.

PipName="PIP-WEB"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static

# Create a virtual network with one subnet

VnetName="VNet1"
VnetPrefix="10.0.0.0/16"
VnetSubnet1Name="Front-End"
VnetSubnet1Prefix="10.0.0.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnet1Name \
--subnet-prefix $VnetSubnet1Prefix

# Create a second subnet within the VNet

VnetSubnet2Name="Back-end"
VnetSubnet2Prefix="10.0.1.0/24"
az network vnet subnet create \
--vnet-name $VnetName \
--resource-group $RgName \
--name $VnetSubnet2Name \
--address-prefix $VnetSubnet2Prefix

# Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
# public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
# You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
# options for NICs, enter the az network nic create -h command.

Nic1Name="NIC-FE"
PrivateIpAddress1="10.0.0.5"

az network nic create \
--name $Nic1Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress1 \
--public-ip-address $PipName

# Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
# VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

Nic2Name="NIC-BE"
PrivateIpAddress2="10.0.1.5"

az network nic create \
--name $Nic2Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet2Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress2

# Create a VM and attach the two NICs.

VmName="WEB"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
# more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
# You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
# only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
# The VM size specified in the following variable supports two NICs.

VmSize="Standard_DS2"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# az vm image list command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file.

SshKeyValue="~/.ssh/id_rsa.pub"

# Before executing the following command, add variable names of additional NICs you may have added to the script that
# you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
# the password you want to configure for the VM.

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $Nic1Name $Nic2Name \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

除了创建具有两个 NIC 的 VM，该脚本还创建：
- 创建单个高级托管磁盘（默认情况下），但对于可以创建的磁盘类型，你可以有其他选择。 有关详细信息，请阅读[使用 Azure CLI 2.0 创建 Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文。
- 一个包含两个子网和单个公共 IP 地址的虚拟网络。 或者，可以使用*现有*虚拟网络、子网、NIC 或公共 IP 地址资源。 若要了解如何使用现有网络资源，而不是创建其他资源，请输入 `az vm create -h`。

## <a name = "validate"></a>验证 VM 创建和 NIC

1. 输入命令 `az resource list --resouce-group Multi-NIC-VM --output table`，查看由脚本创建的资源的列表。 返回的输出中应该有六个资源：两个 NIC、一个磁盘、一个公共 IP 地址、一个虚拟网络和一个虚拟机。
2. 输入命令 `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table`。 在返回的输出中，记下 **IpAddress** 的值，并且 **PublicIpAllocationMethod** 的值是 *Static*。
3. 在运行以下命令前，请删除 <>，将 *Username* 替换为用于脚本中 **Username** 变量的名称，并将 *ipAddress* 替换为上一步中的 **ipAddress**。 输入以下命令以连接到 VM：`ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`。 
4. 连接到 VM 后，运行 `sudo ifconfig` 命令以查看 *eth0* 和 *eth1* 接口。 Azure DHCP 服务器已为每个 NIC 分配了脚本中指定的静态专用 IP 地址。 删除 VM 之前，分配给 NIC 的 IP 和 MAC 地址不会更改。 建议你不更改操作系统内的 IP 寻址，因为它可能会禁用与计算机的连接。 公共 IP 地址不会显示在操作系统中，因为它们是 Azure 基础结构转换到专用 IP 地址或从专用 IP 地址转换的网络地址。

## <a name= "clean-up"></a>删除 VM 和关联的资源

如果生产环境中不会使用此练习中创建的资源，建议将其删除。 VM、公共 IP 地址和磁盘资源在预配后将产生费用。 若要删除此练习中创建的资源，请完成以下步骤：
1. 若要查看资源组中的资源，请运行 `az resource list --resource-group Multi-NIC-VM` 命令。
2. 请确认在资源组中除了本文中脚本创建的资源外没有其他资源。 
3. 若要删除本练习中创建的所有资源，请运行 `az group delete --name Multi-NIC-VM` 命令。 该命令将删除资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

任何网络流量都可流入和流出本文中创建的 VM。 你可以在 NSG 中定义入站和出站规则，以限制可以流入和流出每个网络接口和/或每个子网的流量。 若要了解有关 NSG 的详细信息，请阅读 [NSG 概述](virtual-networks-nsg.md)一文。
