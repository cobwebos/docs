---
title: 在 Azure 虚拟网络中保留公共 IPv6 地址和地址范围
titlesuffix: Azure Virtual Network
description: 了解如何在 Azure 虚拟网络中保留公共 IPv6 地址和地址范围。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595085"
---
# <a name="reserve-public-ipv6-address-prefix"></a>保留公共 IPv6 地址前缀
使用适用于 Azure 虚拟网络（VNet）的 IPv6，可以在 Azure 中使用 IPv6 和 IPv4 连接在虚拟网络中托管应用程序，以及与 Internet 建立连接。 除了保留单个 IPv6 地址外，你还可以保留一个连续的 Azure IPv6 地址范围（称为 IP 前缀）供你使用。 本文介绍如何使用 Azure PowerShell 和 CLI 创建 IPv6 公共 IP 地址和地址范围。

> [!Important]
> Azure 虚拟网络的 IPv6 目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-a-single-reserved-ipv6-public-ip"></a>创建单个保留 IPv6 公共 IP

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

您可以 Azure PowerShell 使用[AzPublicIpAddress 和](/powershell/module/az.network/new-azpublicipaddress)创建单个保留（静态） IPV6 公共 IP 地址，如下所示：

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

 你可以使用[az network 公共 ip create](/cli/azure/network/public-ip)创建单个保留（静态） IPV6 公共 ip Azure CLI 地址，如下所示：
  
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

若要保留 IPv6 前缀，请将 IPv6 的 IP 地址系列添加到用于创建 IPv4 前缀的相同命令。 以下命令将创建一个大小为/125 （8个 IPv6 地址）的前缀。  

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

可以使用带[az network 公共 ip create](/powershell/module/az.network/new-azpublicipprefix) Azure CLI 创建公共 IPv6 地址，如下所示：
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

你可以使用 Azure CLI 创建公共 IPv6 地址，如下所示：

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

 使用 Azure PowerShell 创建公共 IP 时，可以通过添加 `-PublicIpPrefix` 参数，从保留前缀创建静态 IPv6 公共 IP。 下面的示例假定已创建前缀并将其存储在名为的 PowerShell 变量中： *$MyOwnIPv 6prefix*。

```azurepowershell:  
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
- 详细了解[IPv6 地址前缀](ipv6-public-ip-address-prefix.md)。
- 了解有关[IPv6 地址](ipv6-overview.md)的详细信息。
