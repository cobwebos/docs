---
title: 为 VM 配置专用 IP 地址 - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure 命令行接口 (CLI) 为虚拟机配置专用 IP 地址。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4f6a40fde23ee70391c5057762f17ce1eb44123
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423535"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>使用 Azure CLI 为虚拟机配置专用 IP 地址

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍 Resource Manager 部署模型。 还可以[管理经典部署模型中的静态专用 IP 地址](virtual-networks-static-private-ip-classic-cli.md)。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> 以下示例 Azure CLI 命令需要现有的简单环境。 若要运行本文档中所显示的命令，请首先构建[创建 VNet](quick-create-cli.md) 中所述的测试环境。

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>在创建 VM 时指定静态专用 IP 地址

若要在名为 *TestVNet* 的 VNet 的 *FrontEnd* 子网中使用静态专用 IP *192.168.1.101* 创建名为 *DNS01* 的 VM，请完成以下步骤：

1. 如果尚未这样做，请安装并配置最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index#az_login) 登录 Azure 帐户。 

2. 使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 命令，为该 VM 创建公共 IP。 在输出后显示的列表说明了所用的参数。

    > [!NOTE]
    > 可能想要或需要根据环境对此步骤和后续步骤中的变量使用不同的值。
   
    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    预期输出：
   
   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`：要在其中创建公共 IP 的资源组的名称。
   * `--name`：公共 IP 的名称。
   * `--location`：要在其中创建公共 IP 的 Azure 区域。

3. 运行 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 命令，以创建具有静态专用 IP 的 NIC。 在输出后显示的列表说明了所用的参数。 
   
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

4. 运行 [azure vm create](/cli/azure/vm/nic#az_vm_nic_create) 命令，以使用前面创建的公共 IP 和 NIC 创建 VM。 在输出后显示的列表说明了所用的参数。
   
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
   
   基本 [az vm create](/cli/azure/vm#az_vm_create) 参数以外的参数。

   * `--nics`：VM 所附加到的 NIC 的名称。
   
我们建议，除非有必要（例如，[为 Windows VM 分配多个 IP 地址](virtual-network-multiple-ip-addresses-cli.md)时），否则不要以静态方式在 VM 的操作系统中分配已分配给 Azure 虚拟机的专用 IP。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址是同一地址，否则可能会丢失与虚拟机的连接。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>检索 VM 的静态专用 IP 地址信息

运行以下 Azure CLI 命令以观察 *Private IP alloc-method* 和 *Private IP address* 的值：

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

预期输出：

```json
"192.168.1.101"
```

若要显示该 VM 的 NIC 的特定 IP 信息，请专门查询 NIC：

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

输出类似下面这样：

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>从 VM 中删除静态专用 IP 地址

无法在用于Azure 资源管理器部署的 Azure CLI 中删除 NIC 的静态专用 IP 地址。 必须：
- 创建使用动态 IP 的新 NIC
- 将 VM 上的 NIC 设置为新创建的 NIC。 

若要更改上述命令中使用的 VM 的 NIC，请完成以下步骤：

1. 运行 **azure network nic create** 命令，以使用新 IP 地址通过动态 IP 分配创建新 NIC。 由于未指定任何 IP 地址，因此分配方法为“动态”。

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

2. 运行 **azure vm set** 命令，以更改 VM 使用的 NIC。
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
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
    > 如果 VM 大到足以安装多个 NIC，请运行 **azure network nic delete** 命令删除旧 NIC。

## <a name="next-steps"></a>后续步骤

了解如何管理 [IP 地址设置](virtual-network-network-interface-addresses.md)。