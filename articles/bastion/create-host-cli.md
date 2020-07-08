---
title: 使用 Azure CLI 创建堡垒主机 |Azure 堡垒
description: 本文介绍如何创建和删除堡垒主机
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: e7f80bb7f9be2e01aa24090d7305b1a5d882da04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255507"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>使用 Azure CLI 创建 Azure 堡垒主机

本文介绍如何使用 Azure CLI 创建 Azure 堡垒主机。 在虚拟网络中预配 Azure 堡垒服务后，同一虚拟网络中的所有 Vm 都可以使用无缝的 RDP/SSH 体验。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

或者，你可以使用[Azure 门户](bastion-create-host-portal.md)或[Azure PowerShell](bastion-create-host-powershell.md)来创建 Azure 堡垒主机。

## <a name="before-you-begin"></a>准备阶段

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

本部分帮助你使用 Azure CLI 创建新的 Azure 堡垒资源。

1. 创建虚拟网络和 Azure 堡垒子网。 必须使用 name 值**AzureBastionSubnet**创建 Azure 堡垒子网。 此值告知 Azure 要将 Bastion 资源部署到哪个子网。 这不同于网关子网。 必须使用至少/27 或更大子网的子网（/27、/26 等）。 创建不包含任何路由表或委托的**AzureBastionSubnet** 。 如果在**AzureBastionSubnet**上使用网络安全组，请参阅[使用 nsg](bastion-nsg.md)一文。

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. 为 Azure 堡垒创建公共 IP 地址。 公共 IP 是公共 IP 地址，将在其上访问 RDP/SSH 的堡垒资源（通过端口443）。 公共 IP 地址必须与要创建的 Bastion 资源位于同一区域。

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp --sku Standard
   ```

3. 在虚拟网络的 AzureBastionSubnet 中创建新的 Azure 堡垒资源。 创建和部署堡垒资源大约需要5分钟。

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>后续步骤

* 有关其他信息，请参阅[堡垒常见问题解答](bastion-faq.md)。

* 若要在 Azure Bastion 子网中使用网络安全组，请参阅[使用 NSG](bastion-nsg.md)。
