---
title: "控制路由和虚拟设备使用 Azure CLI 1.0 |Microsoft 文档"
description: "了解如何控制路由和虚拟设备使用 Azure CLI 1.0。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2017
ms.author: jdial
ms.openlocfilehash: 5f21bc7a4fcd9507ea9d6b2b752a2328a7b834f0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-10"></a>创建用户定义路由 (UDR) 使用 Azure CLI 1.0

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [模板](virtual-network-create-udr-arm-template.md)
> * [PowerShell （经典）](virtual-network-create-udr-classic-ps.md)
> * [CLI （经典）](virtual-network-create-udr-classic-cli.md)

创建自定义路由和使用 Azure CLI 的虚拟设备。

## <a name="cli-versions-to-complete-the-task"></a>要完成任务的 CLI 版本 

你可以完成该任务使用以下的 CLI 版本之一： 

- [Azure CLI 1.0](#Create-the-UDR-for-the-front-end-subnet) – 我们适用于经典部署模型和资源管理部署模型 （本文） CLI
- [Azure CLI 2.0](virtual-network-create-udr-arm-cli.md) -我们下一代 CLI 用于资源管理部署模型 


[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

下面的示例 Azure CLI 命令需要已根据上述方案创建一个简单的环境。 如果你想要运行本文档中所显示的命令，首先需要构建测试环境通过部署[此模板](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)，单击**部署到 Azure**，替换默认参数值，如有必要，然后按照门户中的说明。


## <a name="create-the-udr-for-the-front-end-subnet"></a>为前端子网创建 UDR
若要创建的路由表和所需的基于上述方案前端子网的路由，请按照下面的步骤。

1. 运行以下命令以创建前端子网的路由表：

    ```azurecli
    azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest
    ```
   
    输出:
   
        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK
   
    参数：
   
   * **-g （或---resource-group）**。 将在其中创建 UDR 的资源组的名称。 对于我们的方案， *TestRG*。
   * **-l （或--位置）**。 将在其中创建新的 UDR 的 azure 区域。 对于我们的方案， *westus*。
   * **-n （或--名称）**。 新的 UDR 的名称。 对于我们的方案， *Udr-frontend*。
2. 运行以下命令以创建路由表中的发送目标至后端子网 (192.168.2.0/24) 所有流量路由到**FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4
    ```
   
    输出:
   
        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK
   
    参数：
   
   * **-r （或--路由表名称）**。 将在其中添加的路由的路由表的名称。 对于我们的方案， *Udr-frontend*。
   * **-a （或---address-prefix）**。 数据包的目标的子网的地址前缀。 对于我们的方案， *192.168.2.0/24*。
   * **-y （或---next-hop-type）**。 类型的对象流量将发送到。 可能的值为*VirtualAppliance*， *VirtualNetworkGateway*， *VNETLocal*， *Internet*，或*无*。
   * **-p (或--下一个跃点 ip 地址**)。 下一个跃点的 IP 地址。 对于我们的方案， *192.168.0.4*。
3. 运行以下命令以将与上面创建的路由表关联**前端**子网：

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    输出:
   
        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK
   
    参数：
   
   * **-e （或--vnet-name）**。 子网所在的 VNet 的名称。 对于我们的方案， *TestVNet*。

## <a name="create-the-udr-for-the-back-end-subnet"></a>为后端子网创建 UDR
若要创建的路由表和路由所需的基于上述方案后端子网，请完成以下步骤：

1. 运行以下命令以创建后端子网的路由表：

    ```azurecli
    azure network route-table create -g TestRG -n UDR-BackEnd -l westus
    ```

2. 运行以下命令以创建路由表中的发送目标至前端子网 (192.168.1.0/24) 所有流量路由到**FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4
    ```

3. 运行以下命令以将路由表与关联**后端**子网：

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>FW1 上启用 IP 转发
若要使用的 NIC 中启用 IP 转发**FW1**，完成以下步骤：

1. 运行该命令之后，请注意的值**启用 IP 转发**。 它应设置为*false*。

    ```azurecli
    azure network nic show -g TestRG -n NICFW1
    ```

    输出:
   
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK
2. 运行以下命令以启用 IP 转发：

    ```azurecli
    azure network nic set -g TestRG -n NICFW1 -f true
    ```
   
    输出:
   
        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK
   
    参数：
   
   * **-f （或--启用 ip 转发）**。 *true* or *false*.

