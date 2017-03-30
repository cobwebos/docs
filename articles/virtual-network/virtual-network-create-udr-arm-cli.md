---
title: "使用 Azure CLI 2.0 控制路由和虚拟设备 | Microsoft 文档"
description: "了解如何使用 Azure CLI 2.0 控制路由和虚拟设备。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 3a85fa624dc55f31822f00910b6d124c1d37323f
ms.lasthandoff: 03/22/2017


---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 创建用户定义的路由 (UDR)

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [模板](virtual-network-create-udr-arm-template.md)
> * [PowerShell（经典部署）](virtual-network-create-udr-classic-ps.md)
> * [CLI（经典部署）](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本 

可以使用以下 CLI 版本之一完成任务： 

- [Azure CLI 1.0](virtual-network-create-udr-arm-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI 
- [Azure CLI 2.0](#Create-the-UDR-for-the-front-end-subnet) - 适用于资源管理部署模型（本文）的下一代 CLI

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

下面的示例 Azure CLI 命令需要一个已经基于上述方案创建的简单环境。 若要运行本文档中所显示的命令，请首先通过部署[此模板](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)构建测试环境，单击“**部署至 Azure**”，根据需要替换默认参数值，然后按照门户中的说明进行操作。


## <a name="create-the-udr-for-the-front-end-subnet"></a>为前端子网创建 UDR
若要根据上述方案为前端子网创建所需的路由表和路由，请按照下面的步骤操作。

1. 使用 [az network route-table create](/cli/azure/network/route-table#create) 命令为前端子网创建路由表：

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    输出：

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. 使用 [az network route-table route create](/cli/azure/network/route-table/route#create) 命令创建一个路由，用以将去往后端子网 (192.168.2.0/24) 的所有流量发送到 **FW1** VM (192.168.0.4)。

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    输出：

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    参数：

    * **--route-table-name**。 要添加路由的路由表的名称。 对于我们的方案，为 *UDR-FrontEnd*。
    * **--address-prefix**。 数据包的目标子网的地址前缀。 对于我们的方案，为 *192.168.2.0/24*。
    * **--next-hop-type**。 要发送的对象流量的类型。 可能的值为 *VirtualAppliance*、*VirtualNetworkGateway*、*VNETLocal*、*Internet* 或 *None*。
    * **--next-hop-ip-address**。 下一个跃点的 IP 地址。 对于我们的方案，为 *192.168.0.4*。

3. 运行 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) 命令来将上面创建的路由表与**前端**子网相关联：

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    输出：

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    参数：
    
    * **--vnet-name**。 子网所在的 VNet 的名称。 对于我们的方案，为 *TestVNet*。

## <a name="create-the-udr-for-the-back-end-subnet"></a>为后端子网创建 UDR

若要根据上述方案创建后端子网所需的路由表和路由，请完成以下步骤：

1. 运行以下命令为后端子网创建路由表：

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. 运行以下命令，在路由表中创建路由，将目标为前端子网 (192.168.1.0/24) 的所有流量发送到 **FW1** VM (192.168.0.4)：

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. 运行以下命令将路由表与 **BackEnd** 子网关联：

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>在 FW1 上启用 IP 转发

若要在 **FW1** 使用的 NIC 中启用 IP 转发，请完成以下步骤：

1. 运行 [az network nic show](/cli/az/network/nic#show) 命令并使用 JMESPATH 筛选器来显示 **Enable IP forwarding** 的当前 **enable-ip-forwarding** 值。 它应设置为 *false*。

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    输出：

        false

2. 运行以下命令以启用 IP 转发：

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    你可以查看流式传输到控制台的输出，或者仅针对特定的 **enableIpForwarding** 值进行重新测试：

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    输出：

        true

    参数：

    **--ip-forwarding**：*true* 或 *false*。


