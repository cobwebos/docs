---
title: "使用 Azure CLI 1.0 创建虚拟网络 | Microsoft Docs"
description: "了解如何使用 Azure CLI 1.0 创建虚拟网络 | Resource Manager。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: acfb9345b789df7664075ef0fb6280beedf0dfb9
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>使用 Azure CLI 创建虚拟网络

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有两个部署模型：Azure Resource Manager 模型和经典模型。 Microsoft 建议通过 Resource Manager 部署模型创建资源。 若要详细了解这两个模型之间的差别，请阅读 [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md)（了解 Azure 部署模型）一文。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 2.0](virtual-networks-create-vnet-arm-cli.md) - 适用于资源管理部署模型的下一代 CLI
- [Azure CLI 1.0](#create-a-virtual-network) – 用于经典部署模型和资源管理部署模型（本文）的 CLI

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>创建虚拟网络

若要使用 Azure CLI 创建虚拟网络，请完成以下步骤：

1. 请按照[安装和配置 Azure CLI](../cli-install-nodejs.md) 文章中的步骤安装和配置 Azure CLI。

2. 创建 VNet 和子网：

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    预期输出：
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    使用的参数：

   * **--vnet**。 要创建的 VNet 的名称。 对于我们的方案，为 *TestVNet*
   * **-e（或 --address-space）**。 VNet 地址空间。 对于我们的方案，为 *192.168.0.0*
   * **-i（或 -cidr）**。 采用 CIDR 格式的网络掩码。 对于我们的方案，为 *16*。
   * **-n（或 --subnet-name）**。 第一个子网的名称。 对于我们的方案，为 *FrontEnd*。
   * **-p（或 --subnet-start-ip）**。 子网或子网地址空间的起始 IP 地址。 对于我们的方案，为 *192.168.1.0*。
   * **-r（或 --subnet-cidr）**。 子网的网络掩码（采用 CIDR 格式）。 对于我们的方案，为 *24*。
   * **-l（或 --location）**。 要在其中创建 VNet 的 Azure 区域 。 对于我们的方案，为*美国中部*。

3. 创建子网：

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    预期输出：

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    使用的参数：

   * **-t（或 --vnet-name）**。 将在其中创建子网的 VNet 的名称。 对于我们的方案，为 *TestVNet*。
   * **-n（或 --name）**。 新子网的名称。 对于我们的方案，为 *BackEnd*。
   * **-a（或 --address-prefix）**。 子网 CIDR 块。 对于我们的方案，为 *192.168.2.0/24*。
   
4. 查看新 VNet 的属性：

    ```azurecli
    azure network vnet show
    ```
   
    预期输出：
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>后续步骤

如何连接：

- 阅读文章[创建 Windows VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md)，将虚拟机 (VM) 连接到虚拟网络。 不要根据这两篇文章的步骤创建 VNet 和子网，可以选择要将 VM 连接到的现有 VNet 和子网。
- 阅读[连接 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 一文，将一个虚拟网络连接到其他虚拟网络。
- 使用站点到站点虚拟专用网络 (VPN) 或 ExpressRoute 线路，将本地网络连接到虚拟网络。 阅读文章[使用站点到站点 VPN 将 VNet 连接到本地网络](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)和[将 VNet 链接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)，了解相关操作方法。
