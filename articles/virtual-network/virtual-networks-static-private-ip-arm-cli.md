---
title: 为 VM 配置专用 IP 地址 - Azure CLI
description: 了解如何使用 Azure 命令行接口 (CLI) 为虚拟机配置专用 IP 地址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: allensu
ms.openlocfilehash: c34ab73422d8dd41feb9da542ed63fdba060fe3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84708155"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>使用 Azure CLI 为虚拟机配置专用 IP 地址


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> 以下示例 Azure CLI 命令需要现有的简单环境。 若要运行本文档中所显示的命令，请首先构建[创建 VNet](quick-create-cli.md) 中所述的测试环境。

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>在创建 VM 时指定静态专用 IP 地址

若要在名为 *TestVNet* 的 VNet 的 *FrontEnd* 子网中使用静态专用 IP *192.168.1.101* 创建名为 *DNS01* 的 VM，请完成以下步骤：

1. 如果尚未这样做，请安装并配置最新的 [Azure CLI](/cli/azure/install-azure-cli)，并使用 [az login](/cli/azure/reference-index) 登录 Azure 帐户。

2. 运行 [az network nic create](/cli/azure/network/nic) 命令，创建具有静态专用 IP 的 NIC。 在输出后显示的列表说明了所用的参数。 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    预期输出：
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    参数：

    * `--private-ip-address`：NIC 的静态专用 IP 地址。
    * `--vnet-name`：要在其中创建 NIC 的 VNet 的名称。
    * `--subnet`：要在其中创建 NIC 的子网的名称。

3. 运行 [azure vm create](/cli/azure/vm/nic) 命令，以使用前面创建的公共 IP 和 NIC 创建 VM。 在输出后显示的列表说明了所使用的参数。
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    预期输出：
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   基本 [az vm create](/cli/azure/vm) 参数之外的参数。

   * `--nics`：VM 附加到的 NIC 的名称。
   
建议你不要静态地在 VM 的操作系统中分配分配给 Azure 虚拟机的专用 IP，除非必要，例如，在 [将多个 IP 地址分配给 WINDOWS VM](virtual-network-multiple-ip-addresses-cli.md)时。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址是同一地址，否则可能会丢失与虚拟机的连接。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>检索 VM 的静态专用 IP 地址信息

运行以下 Azure CLI 命令以观察 *Private IP alloc-method* 和 *Private IP address* 的值：

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

预期输出：

```json
"192.168.1.101"
```

若要显示该 VM 的 NIC 的具体 IP 信息，请具体查询 NIC：

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

输出类似于：

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>从 VM 中删除静态专用 IP 地址

无法在用于Azure 资源管理器部署的 Azure CLI 中删除 NIC 的静态专用 IP 地址。 必须具备以下条件：
- 创建使用动态 IP 的新 NIC
- 在新建的 NIC 的 VM 上设置 NIC。 

若要更改上述命令中使用的 VM 的 NIC，请完成以下步骤：

1. 运行 **azure network nic create** 命令，通过具有新 IP 地址的动态 IP 分配创建新 NIC。 由于未指定任何 IP 地址，因此分配方法为“动态”。

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    预期输出：

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. 运行 **azure vm set** 命令以更改 VM 使用的 NIC。
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    预期输出：
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > 如果 VM 的空间足以拥有多个 NIC，请运行 **azure network nic delete** 命令删除旧版 NIC。

## <a name="next-steps"></a>后续步骤

了解如何管理 [IP 地址设置](virtual-network-network-interface-addresses.md)。
