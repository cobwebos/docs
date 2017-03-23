---
title: "创建虚拟网络 - Azure PowerShell | Microsoft 文档"
description: "了解如何使用 PowerShell 创建虚拟网络"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a31f4f12-54ee-4339-b968-1a8097ca77d3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: d229d6add2c06952eaf662bdacc92a5163b1e945
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-powershell"></a>使用 PowerShell 创建虚拟网络

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有两个部署模型：Azure Resource Manager 模型和经典模型。 Microsoft 建议通过 Resource Manager 部署模型创建资源。 若要详细了解这两个模型之间的差别，请阅读 [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md)（了解 Azure 部署模型）一文。
 
本文说明如何使用 PowerShell 通过资源管理器部署模型创建 VNet。 此外，也可以使用其他工具通过 Resource Manager 创建 VNet，或者从以下列表中选择一个不同的选项，通过经典部署模型创建 VNet：

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

若要使用 PowerShell 创建虚拟网络，请完成以下步骤：

1. 请按[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 文章中的步骤安装和配置 Azure PowerShell。

2. 如有必要，创建一个新的资源组，如下所示。 对于此方案，创建一个名为 *TestRG*的资源组。 有关资源组的详细信息，请访问 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。

    ```powershell   
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    预期输出：

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG    
3. 创建名为 *TestVNet*的新 VNet：

    ```powershell
    New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location centralus
    ```

    预期输出：

        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                   : W/"[Id]"
        ProvisioningState          : Succeeded
        Tags                       : 
        AddressSpace               : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                  }
        DhcpOptions                : {}
        Subnets                    : []
        VirtualNetworkPeerings     : []
4. 将虚拟网络对象存储为变量：

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

   > [!TIP]
   > 可以通过运行 `$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus` 将步骤 3 和步骤 4 结合使用。
   > 

5. 将子网添加到新的 VNet 变量中：

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
    ```

    预期输出：
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings     : []

6. 对要创建的每个子网重复执行上面的步骤 5。 以下命令为方案创建 *BackEnd* 子网：

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
    ```

7. 尽管你创建了子网，但它们当前仅存在于用于检索你在上面的步骤 4 中创建的 VNet 的本地变量中。 若要保存对 Azure 所做的更改，请运行以下命令：

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```
   
    预期输出：
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {
                                  "DnsServers": []
                                }
        Subnets               : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []

## <a name="next-steps"></a>后续步骤

如何连接：

- 通过阅读文章[创建 Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md)， 将虚拟机 (VM) 连接到虚拟网络。 不要根据这两篇文章的步骤创建 VNet 和子网，可以选择要将 VM 连接到的现有 VNet 和子网。
- 阅读[连接 VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) 一文，将一个虚拟网络连接到其他虚拟网络。
- 使用站点到站点虚拟专用网络 (VPN) 或 ExpressRoute 线路，将本地网络连接到虚拟网络。 阅读文章[使用站点到站点 VPN 将 VNet 连接到本地网络](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)和[将 VNet 链接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-arm.md)，了解相关操作方法。

