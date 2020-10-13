---
title: 在 Azure 虚拟网络中保留公共 IPv6 地址和地址范围
titlesuffix: Azure Virtual Network
description: 了解如何在 Azure 虚拟网络中保留公共 IPv6 地址和地址范围。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ac79e1eb5c4f7448dc17804cd8aac3cba582497e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509937"
---
# <a name="reserve-public-ipv6-address-prefix"></a>保留公共 IPv6 地址前缀
Azure 虚拟网络 (VNet) IPv6 可让你通过虚拟网络内部的以及与 Internet 之间的 IPv6 和 IPv4 连接，在 Azure 中托管应用程序。 除了保留单个 IPv6 地址以外，还可以保留连续的 Azure IPv6 地址范围（称作 IP 前缀）供你使用。 本文介绍如何使用 Azure PowerShell 和 CLI 创建 IPv6 公共 IP 地址与地址范围。


## <a name="create-a-single-reserved-ipv6-public-ip"></a>创建单个保留的 IPv6 公共 IP

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

可以在 Azure PowerShell 中使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 创建单个保留的（静态）IPv6 公共 IP 地址，如下所示：

```azurepowershell
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>使用 Azure CLI

 可以在 Azure CLI 中使用 [az network public-ip create](/cli/azure/network/public-ip) 创建单个保留的（静态）IPv6 公共 IP 地址，如下所示：

```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>创建保留的 IPv6 前缀（范围）

若要保留某个 IPv6 前缀，请将 IPv6 的 IP 地址系列添加到用于创建 IPv4 前缀的同一命令。 以下命令将创建大小为 /125（8 个 IPv6 地址）的前缀。

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

可以在 Azure CLI 中使用 [az network public-ip create](/powershell/module/az.network/new-azpublicipprefix) 创建公共 IPv6 地址，如下所示：
```azurepowershell
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>使用 Azure CLI

可按如下所示使用 Azure CLI 创建公共 IPv6 地址：

```azurecli
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>从保留的 IPv6 前缀分配公共 IP 地址

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

 使用 Azure PowerShell 创建公共 IP 时，可以通过添加 `-PublicIpPrefix` 参数，从保留的前缀创建静态 IPv6 公共 IP。 以下示例假设已创建一个前缀并将其存储在名称如下的 PowerShell 变量中： *$myOwnIPv6Prefix*。

```azurepowershell
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>使用 Azure CLI

下面的示例假定已创建前缀，并将其存储在名为的 CLI 变量中： *IPv6PrefixWestUS*。

```azurecli
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>后续步骤
- 详细了解 [IPv6 地址前缀](ipv6-public-ip-address-prefix.md)。
- 详细了解 [IPv6 地址](ipv6-overview.md)。
