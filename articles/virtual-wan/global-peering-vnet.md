---
title: 为 Azure 虚拟 WAN 配置全局 VNet 对等互连 |Microsoft Docs
description: 将不同区域中的 VNet 连接到虚拟 WAN 集线器。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588221"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>为虚拟 WAN 配置全局 VNet 对等互连（跨区域 VNet）

可以将不同区域中的 VNet 连接到虚拟 WAN 集线器。

## <a name="before-you-begin"></a>开始之前

验证是否符合以下条件：

* 跨区域 VNet （轮辐）未连接到其他虚拟 WAN 集线器。 一个辐射只能连接到一个虚拟中心。
* VNet （轮辐）不包含虚拟网络网关（例如，Azure VPN 网关或 ExpressRoute 虚拟网络网关）。 如果 VNet 包含虚拟网络网关，则必须先删除该网关，然后才能将辐射 VNet 连接到中心。

## <a name="register"></a>注册此功能

可以使用 PowerShell 注册此功能。 如果在下面的示例中选择 "试用"，则会打开 Azure Cloud Shell，无需在计算机本地安装 PowerShell cmdlet。 如有必要，可以使用 "AzSubscription-SubscriptionId <subid>" cmdlet 更改订阅。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify"></a>验证注册

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="hub"></a>将 VNet 连接到中心

在此步骤中，将创建中心与跨区域 VNet 之间的对等互连连接。 针对要连接的每个 VNet 重复这些步骤。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。
2. 在虚拟网络连接页上，单击“+添加连接”。
3. 在“添加连接”页上填写以下字段：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”以创建对等互连连接。

## <a name="next-steps"></a>后续步骤

若要了解有关虚拟 WAN 的详细信息，请参阅[虚拟 Wan 概述](virtual-wan-about.md)。