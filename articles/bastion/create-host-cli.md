---
title: 使用 Azure CLI 创建 Bastion 主机 | Azure Bastion
description: 本文介绍如何创建和删除 Bastion 主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: 851ec86feb5244ff43759a7aef2b80876dcfa734
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018536"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>使用 Azure CLI 创建 Azure Bastion 主机

本文介绍如何使用 Azure CLI 创建 Azure Bastion 主机。 部署堡垒后，可以使用 Azure 门户通过浏览器通过其专用 IP 地址连接到 VM。 VM 不需要公共 IP 地址、其他客户端或特殊软件。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。 同一虚拟网络中的所有 Vm 都可以使用无缝 RDP/SSH 体验。

或者，可使用 [Azure 门户](tutorial-create-host-portal.md)或 [Azure PowerShell](bastion-create-host-powershell.md) 创建 Azure Bastion 主机。

## <a name="before-you-begin"></a>准备阶段

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

本部分帮助你使用 Azure CLI 创建新的 Azure Bastion 资源。

> [!NOTE]
> 如示例中所示，为确保将资源放在一起部署，请对每个命令结合使用 `--location` 参数和 `--resource-group`。

1. 创建虚拟网络和 Azure Bastion 子网。 必须使用名称值 AzureBastionSubnet 创建 Azure Bastion 子网。 此值告知 Azure 要将 Bastion 资源部署到哪个子网。 这不同于网关子网。 必须使用至少为 /27 或更大（/27、/26 等）的子网。 创建不包含任何路由表或委托的 AzureBastionSubnet。 如果使用 AzureBastionSubnet 上的网络安全组，请参阅[使用 NSG](bastion-nsg.md) 一文。

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. 为 Azure Bastion 创建一个公共 IP 地址。 此公共 IP 是将在其上访问 RDP/SSH（通过端口 443）的 Bastion 资源的公共 IP 地址。 公共 IP 地址必须与要创建的 Bastion 资源位于同一区域。

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. 在虚拟网络的 AzureBastionSubnet 中创建新的 Azure Bastion 资源。 创建和部署 Bastion 资源大约需要 5 分钟。

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>后续步骤

* 连接到虚拟机。
   * [Linux VM](bastion-connect-vm-ssh.md)
   * [Windows VM](bastion-connect-vm-rdp.md)

