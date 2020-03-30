---
title: 在 Azure 虚拟网络中添加或删除子网委托
titlesuffix: Azure Virtual Network
description: 了解如何在 Azure 中添加或删除为服务委托的子网。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201860"
---
# <a name="add-or-remove-a-subnet-delegation"></a>添加或删除子网委托

子网委派为服务提供了显式权限，以便能够在部署服务时使用唯一标识符在子网中创建服务专属资源。 本文介绍如何添加或删除为 Azure 服务委托的子网。

## <a name="portal"></a>门户

### <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

### <a name="create-the-virtual-network"></a>创建虚拟网络

在本节中，您将创建一个虚拟网络和子网，稍后将委托给 Azure 服务。

1. 在屏幕的左上角，选择 **"创建资源** > **网络** > **虚拟网络**"。
1. 在“创建虚拟网络”**** 中，输入或选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    | “属性” | 输入 *MyVirtualNetwork*。 |
    | 地址空间 | 输入 *10.0.0.0/16*。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 myResourceGroup，然后选择“确定”**********。 |
    | 位置 | 选择**东美国**。|
    | 子网 - 名称 | 输入 *mySubnet*。 |
    | 子网 - 地址范围 | 输入 *10.0.0.0/24*。 |
    |||
1. 将其余部分保留为默认值，然后选择 **"创建**"。

### <a name="permissions"></a>权限

如果没有创建要委托给 Azure 服务的子网，则需要以下权限：`Microsoft.Network/virtualNetworks/subnets/write`。

内置的[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色也包含必需的权限。

### <a name="delegate-a-subnet-to-an-azure-service"></a>将子网委托给 Azure 服务

在此部分，我们将上一部分创建的子网委托给 Azure 服务。

1. 在门户的搜索栏中，输入 *myVirtualNetwork*。 当“myVirtualNetwork”出现在搜索结果中时，将其选中。****
2. 在搜索结果中，选择*我的虚拟网络*。
3. 选择"**设置"** 下的**子网**，然后选择 **"我的子网**"。
4. 在*mySubnet*页上，对于**子网委派**列表，从**委托子网下列出的服务中选择服务**（例如 **，Microsoft.DBforPostgreSQL/serverv2）。**  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>从 Azure 服务中删除子网委托

1. 在门户的搜索栏中，输入 *myVirtualNetwork*。 当“myVirtualNetwork”出现在搜索结果中时，将其选中。****
2. 在搜索结果中，选择*我的虚拟网络*。
3. 选择"**设置"** 下的**子网**，然后选择 **"我的子网**"。
4. 在 *"我的子网*"页中，对于**子网委派**列表，从**委托子网**下列出的服务中选择 **"无**"到服务 。 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您决定在本地安装和使用 Azure CLI，则本文要求您使用 Azure CLI 版本 2.0.28 或更高版本。 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-a-resource-group"></a>创建资源组
使用 [az group create](https://docs.microsoft.com/cli/azure/group) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

下面的示例在**东部**位置创建名为**myResourceGroup**的资源组：

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>创建虚拟网络
使用 az 网络 vnet 创建，在**myResourceGroup**中创建名为**myVnet**的子网，创建名为**myVnet**的虚拟[网络](https://docs.microsoft.com/cli/azure/network/vnet)。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>权限

如果没有创建要委托给 Azure 服务的子网，则需要以下权限：`Microsoft.Network/virtualNetworks/subnets/write`。

内置的[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色也包含必需的权限。

### <a name="delegate-a-subnet-to-an-azure-service"></a>将子网委托给 Azure 服务

在此部分，我们将上一部分创建的子网委托给 Azure 服务。 

使用[az 网络 vnet 子网更新](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)更新名为**mySubnet**的子网，并委派到 Azure 服务。  在此示例中，**Microsoft.DBforPostgreSQL/serversv2** 用于示例委托：

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

要验证已应用委派，请使用[az 网络 vnet 子网显示](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)。 验证服务是否委托给属性**服务名称**下的子网：

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>从 Azure 服务中删除子网委托

使用[az 网络 vnet 子网更新](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)从名为**mySubnet**的子网中删除委派：

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
要验证已删除委派，请使用[az 网络 vnet 子网显示](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)。 验证服务从属性**服务名称**下的子网中删除：

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
来自命令的输出为空括号：
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>连接到 Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>创建资源组
使用 [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

下面的示例在*东部*位置创建名为*myResourceGroup*的资源组：

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>创建虚拟网络

使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) 在 **myResourceGroup** 中创建名为 **myVnet** 的虚拟网络，并使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 创建名为 **mySubnet** 的子网。 虚拟网络的 IP 地址空间是 **10.0.0.0/16**。 虚拟网络中的子网是 **10.0.0.0/24**。  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>权限

如果没有创建要委托给 Azure 服务的子网，则需要以下权限：`Microsoft.Network/virtualNetworks/subnets/write`。

内置的[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色也包含必需的权限。

### <a name="delegate-a-subnet-to-an-azure-service"></a>将子网委托给 Azure 服务

在此部分，我们将上一部分创建的子网委托给 Azure 服务。 

请使用 [Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) 通过名为 **myDelegation** 的委托（委托到 Azure 服务）对名为 **mySubnet** 的子网进行更新。  在此示例中，**Microsoft.DBforPostgreSQL/serversv2** 用于示例委托：

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
使用 [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) 验证委托：

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>从 Azure 服务中删除子网委托

使用 [Remove-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) 从名为 **mySubnet** 的子网中删除委托：

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
使用 [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) 验证是否已删除委托：

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>后续步骤
- 了解如何[在 Azure 中管理子网](virtual-network-manage-subnet.md)。
