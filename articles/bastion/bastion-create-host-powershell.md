---
title: 使用 Azure Powershell 创建堡垒主机 |Microsoft Docs
description: 本文介绍如何创建 Azure Bastion 主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: c62ac014513f3e93a04008af06ef8ffe5008ed2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744249"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>使用 Azure PowerShell 创建 Azure Bastion 主机

本文介绍如何使用 PowerShell 创建 Azure Bastion 主机。 在虚拟网络中预配 Azure Bastion 服务后，即可在该虚拟网络中的所有 VM 上获得无缝的 RDP/SSH 体验。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

或者，可使用 [Azure 门户](bastion-create-host-portal.md)创建 Azure Bastion 主机。

## <a name="before-you-begin"></a>准备阶段

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

本部分帮助你使用 Azure PowerShell 创建新的 Azure Bastion 资源。

1. 创建虚拟网络和 Azure Bastion 子网。 必须使用名称值 AzureBastionSubnet 创建 Azure Bastion 子网。 此值告知 Azure 要将 Bastion 资源部署到哪个子网。 这不同于网关子网。 必须使用至少为 /27 或更大（/27、/26 等）的子网。 创建不包含任何路由表或委托的 AzureBastionSubnet。 如果使用 AzureBastionSubnet 上的网络安全组，请参阅[使用 NSG](bastion-nsg.md) 一文。

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. 为 Azure Bastion 创建一个公共 IP 地址。 此公共 IP 是将在其上访问 RDP/SSH（通过端口 443）的 Bastion 资源的公共 IP 地址。 公共 IP 地址必须与要创建的 Bastion 资源位于同一区域。

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. 在虚拟网络的 AzureBastionSubnet 中创建新的 Azure Bastion 资源。 创建和部署 Bastion 资源大约需要 5 分钟。

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>后续步骤

* 有关其他信息，请参阅 [Bastion 常见问题解答](bastion-faq.md)。

* 若要在 Azure Bastion 子网中使用网络安全组，请参阅[使用 NSG](bastion-nsg.md)。
