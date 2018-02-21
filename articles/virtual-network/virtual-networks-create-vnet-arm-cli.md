---
title: "创建虚拟网络 - Azure CLI | Microsoft Docs"
description: "了解如何使用 Azure CLI 创建虚拟网络。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72ed9ecd7a4c8e846818f7a19ad25c566fa57f64
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>使用 Azure CLI 创建虚拟网络

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有两个部署模型：Azure 资源管理器模型和经典模型。 Microsoft 建议通过 Resource Manager 部署模型创建资源。 若要详细了解这两个模型之间的差别，请阅读 [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md)（了解 Azure 部署模型）一文。

此外，也可以使用其他工具通过资源管理器创建虚拟网络，或者从以下列表中选择一个不同的选项，通过经典部署模型创建虚拟网络：

> [!div class="op_single_selector"]
> * [门户](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [模板](virtual-networks-create-vnet-arm-template-click.md)
> * [门户 经典）](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell（经典）](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI（经典）](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>创建虚拟网络

若要使用 Azure CLI 创建虚拟网络，请完成以下步骤：

1. 安装并配置最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/#az_login) 登录 Azure 帐户。

2. 使用具有 `--name` 和 `--location` 参数的 [az group create](/cli/azure/group#az_group_create) 命令创建 VNet 的资源组：

    ```azurecli
    az group create --name TestRG --location centralus
    ```

3. 创建 VNet 和子网：

    ```azurecli
    az network vnet create \
    --name TestVNet \
    --resource-group TestRG \
    --location centralus \
    --address-prefix 192.168.0.0/16 \
    --subnet-name FrontEnd \
    --subnet-prefix 192.168.1.0/24
    ```

    预期输出：
    
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
            "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "TestRG"
            }
            ]
            }
    }
    ```

    使用的参数：

    - `--name TestVNet`：要创建的 VNet 的名称。
    - `--resource-group TestRG`：# 控制资源的资源组名称。 
    - `--location centralus`：要部署到的位置。
    - `--address-prefix 192.168.0.0/16`：地址前缀和块。  
    - `--subnet-name FrontEnd`：子网的名称。
    - `--subnet-prefix 192.168.1.0/24`：地址前缀和块。

    若要列出要在下一个命令中使用的基本信息，可以使用[查询筛选器](/cli/azure/query-az-cli2)对 VNet 进行查询：

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    这会生成以下输出：

        Where      Name      Group

        centralus  TestVNet  TestRG

4. 创建子网：

    ```azurecli
    az network vnet subnet create \
    --address-prefix 192.168.2.0/24 \
    --name BackEnd \
    --resource-group TestRG \
    --vnet-name TestVNet
    ```

    预期输出：

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "TestRG",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    使用的参数：

    - `--address-prefix 192.168.2.0/24`：子网 CIDR 块。
    - `--name BackEnd`：新子网的名称。
    - `--resource-group TestRG`：资源组。
    - `--vnet-name TestVNet`：所拥有的 VNet 的名称。

5. 查询新 VNet 的属性：

    ```azurecli
    az network vnet show \
    -g TestRG \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```

    预期输出：

        Name      Where      Group    Status       SubnetCount

        TestVNet  centralus  TestRG   Succeeded              2

6. 查询子网的属性：

    ```azurecli
    az network vnet subnet list \
    -g TestRG \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    预期输出：

        Name      CIDR            Status

        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>后续步骤

如何连接：

- 阅读文章[创建 Windows VM](../virtual-machines/linux/quick-create-cli.md)，将虚拟机 (VM) 连接到虚拟网络。 不要根据这两篇文章的步骤创建 VNet 和子网，可以选择要将 VM 连接到的现有 VNet 和子网。
- 阅读[连接 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 一文，将一个虚拟网络连接到其他虚拟网络。
- 使用站点到站点虚拟专用网络 (VPN) 或 ExpressRoute 线路，将本地网络连接到虚拟网络。 阅读文章[使用站点到站点 VPN 将 VNet 连接到本地网络](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)和[将 VNet 链接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)，了解相关操作方法。