---
title: 为 Azure 虚拟 WAN 配置全局 VNet 对等互连 |微软文档
description: 将不同区域中的 VNet 连接到虚拟广域网中心。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588221"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>为虚拟 WAN 配置全局 VNet 对等互连（跨区域 VNet）

您可以将不同区域中的 VNet 连接到虚拟广域网中心。

## <a name="before-you-begin"></a>开始之前

验证是否符合以下条件：

* 跨区域 VNet（分支）未连接到另一个虚拟广域网中心。 分支只能连接到一个虚拟中心。
* VNet（分支）不包含虚拟网络网关（例如，Azure VPN 网关或 ExpressRoute 虚拟网络网关）。 如果 VNet 包含虚拟网络网关，则必须在将分支 VNet 连接到集线器之前删除网关。

## <a name="register-this-feature"></a><a name="register"></a>注册此功能

您可以使用 PowerShell 注册此功能。 如果从下面的示例中选择"试用"，Azure 云壳将打开，并且无需在本地将 PowerShell cmdlet 安装到您的计算机。 如有必要，您可以使用"选择-订阅 -订阅 Id" <subid>cmdlet 更改订阅。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>验证注册

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>将 VNet 连接到集线器

在此步骤中，您将在集线器和跨区域 VNet 之间创建对等互连连接。 针对要连接的每个 VNet 重复这些步骤。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。****
2. 在虚拟网络连接页上，单击“+添加连接”。****
3. 在“添加连接”页上填写以下字段****：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”以创建对等互连连接。****

## <a name="next-steps"></a>后续步骤

要了解有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。