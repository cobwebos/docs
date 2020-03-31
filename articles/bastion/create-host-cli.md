---
title: 使用 Azure CLI 创建堡垒主机 |Azure 堡垒
description: 在本文中，了解如何创建和删除堡垒主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: cherylmc
ms.openlocfilehash: 9e216bf2bb8e2e605723256f0511b9bdb63083b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337566"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>使用 Azure CLI 创建 Azure 堡垒主机

本文介绍如何使用 Azure CLI 创建 Azure 堡垒主机。 在虚拟网络中预配 Azure Bastion 服务后，同一虚拟网络中的所有 VM 都可以使用无缝的 RDP/SSH 体验。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

或者，可以使用[Azure 门户](bastion-create-host-portal.md)或使用[Azure PowerShell](bastion-create-host-powershell.md)创建 Azure 堡垒主机。

## <a name="before-you-begin"></a>开始之前

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

本节可帮助您使用 Azure CLI 创建新的 Azure 堡垒资源。

1. 创建虚拟网络和 Azure 堡垒子网。 您必须使用名称值**Azure Bastion 子网**创建 Azure 堡垒子网。 此值使 Azure 知道要将堡垒资源部署到哪个子网。 这与网关子网不同。 您必须使用至少 /27 或更大的子网（/27、/26 等）的子网。 创建**AzureBastion Subnet，** 没有任何路由表或授权。 如果在**AzureBastionSubnet**上使用网络安全组，请参阅[使用 NSG](bastion-nsg.md)的文章。

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. 为 Azure 堡垒创建公共 IP 地址。 公共 IP 是公共 IP 地址，是将访问 RDP/SSH 的堡垒资源（通过端口 443）。 公共 IP 地址必须与您正在创建的堡垒资源位于同一区域。

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. 在虚拟网络的 Azure Bastion Subnet 中创建新的 Azure 堡垒资源。 创建和部署堡垒资源大约需要 5 分钟。

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>后续步骤

* 有关详细信息，请阅读[堡垒常见问题解答](bastion-faq.md)。

* 要将网络安全组与 Azure 堡垒子网一起使用，请参阅[使用 NSG。](bastion-nsg.md)
