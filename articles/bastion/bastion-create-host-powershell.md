---
title: 使用 Azure Powershell 创建堡垒主机 |Microsoft Docs
description: 本文介绍如何创建 Azure 堡垒主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 6cae6d258da2ddf0c3bfaade65ae74f1201b67b7
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121074"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>使用 Azure PowerShell 创建 Azure 堡垒主机

本文介绍如何创建 Azure 堡垒主机。 在虚拟网络中预配 Azure 堡垒服务后，同一虚拟网络中的所有 Vm 都可以使用无缝 RDP/SSH 体验。 此部署是按每个虚拟网络执行的，不是按每个订阅/帐户或虚拟机操作的。

## <a name="before-you-begin"></a>开始之前

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="createhost"></a>创建堡垒主机

本部分帮助你使用 Azure PowerShell 创建新的 Azure 堡垒资源。

1. 创建虚拟网络和 Azure 堡垒子网。 必须使用 name 值**AzureBastionSubnet**创建 Azure 堡垒子网。 此值允许 Azure 知道要将堡垒资源部署到哪个子网。 这不同于网关子网。 必须至少使用一个/27 或更大的子网（/27、/26 等）。 创建不包含任何路由表或委托的**AzureBastionSubnet** 。 在**AzureBastionSubnet**上使用网络安全组时，请参阅[使用 nsg](bastion-nsg.md)。

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName " myBastionRG " -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. 为 Azure 堡垒创建公共 IP 地址。 公共 IP 是公共 IP 地址，将在其上访问 RDP/SSH 的堡垒资源（通过端口443）。 公共 IP 地址必须与要创建的堡垒资源位于同一区域。

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. 在虚拟网络的 AzureBastionSubnet 中创建新的 Azure 堡垒资源。 创建和部署堡垒资源大约需要5分钟。

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName " myBastionRG " -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)。
