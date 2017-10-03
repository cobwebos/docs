---
title: "使用 Azure CLI 2.0 创建具有多个 IP 地址的 VM | Microsoft 文档"
description: "了解如何使用 Azure CLI 2.0 | Resource Manager 将多个 IP 地址分配给虚拟机。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
ms.translationtype: HT
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0e9b2ef89ca39a7988a7b2573496a605dfc604b4
ms.contentlocale: zh-cn
ms.lasthandoff: 04/03/2017

---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 将多个 IP 地址分配给虚拟机

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

本文介绍如何使用 Azure CLI 2.0 通过 Azure Resource Manager 部署模型创建虚拟机 (VM)。 无法将多个 IP 地址分配到通过经典部署模型创建的资源。 若要详细了解 Azure 部署模型，请阅读 [Understand deployment models](../resource-manager-deployment-model.md)（了解部署模型）一文。

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>创建具有多个 IP 地址的 VM

可以使用 Azure CLI 2.0（本文）或 [Azure CLI 1.0](virtual-network-multiple-ip-addresses-cli-nodejs.md) 完成此任务。 根据需要更改环境值。 下面的步骤说明如何根据方案中所述，创建具有多个 IP 地址的示例 VM。 根据实现的需要，需要更改 "" 中的变量值和 IP 地址类型。 

1. 安装 [Azure CLI 2.0](/cli/azure/install-az-cli2)（如果尚未安装）。
2. 通过完成[为 Linux VM 创建 SSH 公钥和私钥对](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步骤创建适用于 Linux VM 的 SSH 公钥和私钥对。
3. 从命令行界面使用命令 `az login` 登录，并选择要使用的订阅。
4. 通过在 Linux 或 Mac 计算机上执行以下脚本创建 VM。 该脚本创建一个资源组、一个虚拟网络 (VNet)、一个具有三个 IP 配置的 NIC 和一个附加有两个 NIC 的 VM。 NIC、公共 IP 地址、虚拟网络和 VM 资源必须存在于同一位置和同一订阅中。 虽然资源不必都存在于同一资源组中，但是在以下脚本中资源都存在于同一资源组中。

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP adresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

azure network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes rticle. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location estcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the utput returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

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
```

除了创建具有附带 3 个 IP 配置的 NIC 的 VM，该脚本还创建：

- 创建单个高级托管磁盘（默认情况下），但对于可以创建的磁盘类型，可以有其他选择。 有关详细信息，请阅读[使用 Azure CLI 2.0 创建 Linux VM](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文。
- 一个包含 1 个子网和 2 个公共 IP 地址的虚拟网络。 或者，可以使用*现有*虚拟网络、子网、NIC 或公共 IP 地址资源。 若要了解如何使用现有网络资源，而不是创建其他资源，请输入 `az vm create -h`。

公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 在一个订阅中可以使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。

创建 VM 后，输入 `az network nic show --name MyNic1 --resource-group myResourceGroup` 命令以查看 NIC 配置。 输入 `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` 以查看与 NIC 关联的 IP 配置的列表

将专用 IP 地址添加到 VM 操作系统，只需完成本文[将 IP 地址添加到 VM 操作系统](#os-config)部分针对操作系统的步骤即可。

## <a name="add"></a>将 IP 地址添加到 VM

完成以下步骤可将其他专用和公共 IP 地址添加到现有 NIC。 这些示例是基于本文中所述的[方案](#Scenario)制作的。

1. 打开命令行界面，然后在单个会话中完成本部分中的剩余步骤。 如果尚未安装并配置 Azure CLI，请完成 [Azure CLI 2.0 安装](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json)一文中的步骤，并使用 `az-login` 命令登录到 Azure 帐户。

2. 根据要求完成以下部分之一中的步骤：

    **添加专用 IP 地址**
    
    要将专用 IP 地址添加到 NIC，必须使用以下命令创建 IP 配置。 静态 IP 地址必须是未使用的子网地址。

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    使用唯一的配置名称和专用 IP 地址创建任意数目的配置（适用于具有静态 IP 地址的配置）。

    **添加公共 IP 地址**
    
    可以通过将某个公共 IP 地址关联到新的 IP 配置或现有 IP 配置，来添加该地址。 根据需要完成以下部分之一中的步骤。

    公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 在一个订阅中可以使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。

    - **将资源关联到新的 IP 配置**
    
        每当在新 IP 配置中添加公共 IP 地址时，还必须添加一个专用 IP 地址，因为所有 IP 配置都必须有专用 IP 地址。 可以添加现有的公共 IP 地址资源，或新建一个。 若要新建此类资源，请输入以下命令：
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        若要新建具有静态专用 IP 地址和关联的 myPublicIP3 公共 IP 地址资源的 IP 配置，请输入下面的命令：

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **将资源关联到现有 IP 配置**公共 IP 地址资源只能关联到尚未与任何公共 IP 地址资源关联的 IP 配置。 输入以下命令即可确定某个 IP 配置是否具有关联的公共 IP 地址：

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        返回的输出：
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        由于输出中 *IpConfig-3* 的 **PublicIpAddressId** 列为空白，因此，当前没有公共 IP 地址资源与其关联。 可将现有公共 IP 地址资源添加到 IpConfig-3，或输入以下命令来创建一个：

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        输入以下命令，将公共 IP 地址资源关联到名为 *IPConfig-3* 的现有 IP 配置：
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. 输入以下命令，查看分配给 NIC 的专用 IP 地址和公共 IP 地址资源 ID：

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    返回的输出： <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. 根据本文[将 IP 地址添加到 VM 操作系统](#os-config)部分中的说明，将添加到 NIC 的专用 IP 地址添加到 VM 操作系统。 请勿向操作系统添加公共 IP 地址。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

