---
title: 使用 Azure Powershell 创建堡垒主机 |Microsoft Docs
description: 本文介绍如何创建 Azure 堡垒主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: c3e4c2f2bac45f2e366764473a34b0536bb4cc44
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990430"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>使用 Azure PowerShell 创建 Azure 堡垒主机

本文介绍如何使用 PowerShell 创建 Azure 堡垒主机。 在虚拟网络中预配 Azure 堡垒服务后，同一虚拟网络中的所有 Vm 都可以使用无缝的 RDP/SSH 体验。 Azure 堡垒部署按虚拟网络，而不是按订阅/帐户或虚拟机。

还可以选择使用[Azure 门户](bastion-create-host-portal.md)创建 Azure 堡垒主机。

## <a name="before-you-begin"></a>开始之前

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="createhost"></a>创建堡垒主机

本部分帮助你使用 Azure PowerShell 创建新的 Azure 堡垒资源。

1. 创建虚拟网络和 Azure 堡垒子网。 必须使用 name 值**AzureBastionSubnet**创建 Azure 堡垒子网。 此值允许 Azure 知道要将堡垒资源部署到哪个子网。 这不同于网关子网。 必须使用至少/27 或更大子网的子网（/27、/26 等）。 创建不包含任何路由表或委托的**AzureBastionSubnet** 。 如果在**AzureBastionSubnet**上使用网络安全组，请参阅[使用 nsg](bastion-nsg.md)一文。

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

* 有关其他信息，请参阅[堡垒常见问题解答](bastion-faq.md)。

* 若要在 Azure 堡垒子网中使用网络安全组，请参阅[使用 nsg](bastion-nsg.md)。
