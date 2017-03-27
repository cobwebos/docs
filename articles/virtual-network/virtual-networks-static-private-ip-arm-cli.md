---
title: "为 VM 配置专用 IP 地址 - Azure CLI 2.0 | Microsoft 文档"
description: "了解如何使用 Azure 命令行接口 (CLI) 2.0 为虚拟机配置专用 IP 地址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: ea54b413b92a4d4e312b741ce42090c77de0e6f5
ms.lasthandoff: 02/28/2017


---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 为虚拟机配置专用 IP 地址

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]


## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本 

可以使用以下 CLI 版本之一完成任务： 

- [Azure CLI 1.0](virtual-networks-static-private-ip-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI 
- [Azure CLI 2.0](#specify-a-static-private-ip-address-when-creating-a-vm) - 适用于资源管理部署模型（本文）的下一代 CLI

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍资源管理器部署模型。 还可以[管理经典部署模型中的静态专用 IP 地址](virtual-networks-static-private-ip-classic-cli.md)。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> 下面的示例 Azure CLI 2.0 命令需要已创建简单的环境。 若要运行本文档中所显示的命令，请首先构建[创建 VNet](virtual-networks-create-vnet-arm-cli.md) 中所述的测试环境。

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>在创建 VM 时指定静态专用 IP 地址

若要在名为 *TestVNet* 的 VNet 的 *FrontEnd* 子网中使用静态专用 IP *192.168.1.101* 创建名为 *DNS01* 的 VM，请按照以下步骤进行操作：

1. 如果尚未这样做，请安装并配置最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/#login) 登录 Azure 帐户。 

2. 使用 [az network public-ip create](/cli/azure/network/public-ip#create) 命令，为该 VM 创建公共 IP。 在输出后显示的列表说明了所用的参数。

    > [!NOTE]
    > 你可能想要或需要根据环境对此步骤和后续步骤中的变量使用不同的值。
   
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

3. 运行 [az network nic create](/cli/azure/network/nic#create) 命令，以创建具有静态专用 IP 的 NIC。 在输出后显示的列表说明了所用的参数。 
   
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
    
    Parameters:

    * `--private-ip-address`: Static private IP address for the NIC.
    * `--vnet-name`: Name of the VNet in wihch to create the NIC.
    * `--subnet`: Name of the subnet in which to create the NIC.

4. Run the [azure vm create](/cli/azure/vm/nic#create) command to create the VM using the public IP and NIC created above. The list shown after the output explains the parameters used.
   
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
   
   基本 [az vm create](/cli/azure/vm#create) 参数以外的参数。

   * `--nics`：VM 所附加到的 NIC 的名称。
   

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>检索 VM 的静态专用 IP 地址信息

若要查看创建的静态专用 IP 地址，请运行以下 Azure CLI 命令并观察 *Private IP alloc-method* 和 *Private IP address* 的值：

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

无法在用于 Resource Manager 部署的 Azure CLI 中删除 NIC 的静态专用 IP 地址。 你必须：
- 创建使用动态 IP 的新 NIC
- 将 VM 上的 NIC 设置为新创建的 NIC。 

若要更改上述命令使用的 VM 的 NIC，请执行以下步骤。

1. 运行 **azure network nic create** 命令，以使用新 IP 地址通过动态 IP 分配创建新 NIC。 请注意，由于未指定任何 IP 地址，因此分配方法为“动态”。

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
* 了解[保留公共 IP](virtual-networks-reserved-public-ip.md) 地址。
* 了解[实例层级公共 IP (ILPIP) 地址](virtual-networks-instance-level-public-ip.md)。
* 查阅[保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)。


