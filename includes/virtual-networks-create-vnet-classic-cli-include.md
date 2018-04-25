---
title: include 文件
description: include 文件
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 749cc5a5e5b8417abe602b7e37c103a26cc4dc03
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>如何使用 Azure CLI 创建经典 VNet
可以使用 Azure CLI 从运行 Windows、Linux 或 OSX 的任何计算机上的命令提示符管理 Azure 资源。

1. 如果从未使用过 Azure CLI，请参阅[安装和配置 Azure CLI](../articles/cli-install-nodejs.md)，并按照说明进行操作，直到选择 Azure 帐户和订阅。
2. 若要创建 VNet 和子网，请运行 **azure network vnet create** 命令：
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    预期输出：
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**。 要创建的 VNet 的名称。 对于该方案，为 *TestVNet*
   * **-e（或 --address-space）**。 VNet 地址空间。 对于该方案，为 *192.168.0.0*
   * **-i（或 -cidr）**。 采用 CIDR 格式的网络掩码。 对于该方案，为 *16*。
   * **-n（或 --subnet-name）**。 第一个子网的名称。 对于该方案，为 *FrontEnd*。
   * **-p（或 --subnet-start-ip）**。 子网或子网地址空间的起始 IP 地址。 对于该方案，为 *192.168.1.0*。
   * **-r（或 --subnet-cidr）**。 子网的网络掩码（采用 CIDR 格式）。 对于该方案，为 *24*。
   * **-l（或 --location）**。 要在其中创建 VNet 的 Azure 区域 。 对于该方案，为 *Central US*。
3. 若要创建子网，请运行 **azure network vnet subnet create** 命令：
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    上述命令的预期输出：
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t（或 --vnet-name）**。 将在其中创建子网的 VNet 的名称。 对于该方案，为 *TestVNet*。
   * **-n（或 --name）**。 新子网的名称。 对于该方案，为 *BackEnd*。
   * **-a（或 --address-prefix）**。 子网 CIDR 块。 对于该方案，为 *192.168.2.0/24*。
4. 若要查看新 vnet 的属性，请运行 **azure network vnet show** 命令：
   
            azure network vnet show
   
    上述命令的预期输出：
   
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

