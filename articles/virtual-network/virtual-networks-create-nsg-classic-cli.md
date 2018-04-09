---
title: 使用 Azure CLI 1.0 创建网络安全组（经典）| Microsoft Docs
description: 了解如何使用 Azure CLI 1.0 创建和部署网络安全组（经典）。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: 67b959378521e5a456a2e464d6dd04bbdcab4f30
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 创建网络安全组（经典）
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍经典部署模型。 还可以[在 Resource Manager 部署模型中创建 NSG](tutorial-filter-network-traffic-cli.md)。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

下面的示例 Azure CLI 命令需要一个已经基于方案创建的简单环境。 如果想要运行本文档中所显示的命令，首先通过[创建 VNet](virtual-networks-create-vnet-classic-cli.md) 构建测试环境。

## <a name="create-an-nsg-for-the-front-end-subnet"></a>为前端子网创建 NSG

1. 如果从未使用过 Azure CLI，请参阅[安装和配置 Azure CLI](../cli-install-nodejs.md)。
2. 切换到经典模式：

    ```azurecli
    azure config mode asm
    ```   

3. 创建 NSG：
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. 创建一个允许从 Internet 访问端口 3389 (RDP) 的安全规则：
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. 创建一个允许从 Internet 访问端口 80 (HTTP) 的规则：
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. 将 NSG 关联到前端子网：
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>为后端子网创建 NSG

1. 创建 NSG：
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. 创建一个允许从前端子网访问端口 1433 (SQL) 的规则：
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. 创建一个拒绝访问 Internet 的规则：
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. 将 NSG 关联到后端子网：
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```