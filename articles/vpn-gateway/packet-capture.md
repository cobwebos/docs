---
title: Azure VPN 网关：配置数据包捕获
description: 了解可在 VPN 网关上使用的数据包捕获功能。
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75353515"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>为 VPN 网关配置数据包捕获

连接和性能相关的问题往往很复杂，缩小其原因排查范围需要花费大量的时间和精力。 数据包捕获功能有助于大大缩短将问题范围缩小到特定网络部分所花费的时间，例如，问题是在网络的客户一端、网络的 Azure 一端，还是在两者之间的某个位置发生。 缩小问题的范围后，调试和采取补救措施就要高效得多。

可以使用一些常用的工具来获取数据包捕获。 但是，使用这些工具获取相关的数据包捕获往往比较繁琐，尤其是流量较高的情况下。 VPN 网关数据包捕获提供的筛选功能已成为一项主要优势。 除了常用的数据包捕获工具以外，也可以使用 VPN 网关数据包捕获。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN 网关数据包捕获筛选功能

VPN 网关数据包捕获可在网关上运行，或者在特定的连接上运行，具体取决于客户的需求。 还可以同时在多个隧道上运行数据包捕获。 可以捕获单向或双向流量、IKE 和 ESP 流量以及内部数据包，并可对 VPN 网关进行筛选。

查明高流量相关问题时，使用 5 元组筛选器（源子网、目标子网、源端口、目标端口、协议）和 TCP 标志（SYN、ACK、FIN、URG、PSH、RST）将很有帮助。

运行数据包捕获时，每个属性只能使用一个选项。

## <a name="setup-packet-capture-using-powershell"></a>使用 PowerShell 设置数据包捕获

请参阅以下示例来了解如何使用 PowerShell 命令启动和停止数据包捕获。 有关参数选项（例如，如何创建筛选器）的详细信息，请参阅此 PowerShell [文档](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>启动 VPN 网关的数据包捕获

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

可选参数 **-FilterData** 可用于应用筛选器。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>停止 VPN 网关的数据包捕获

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>启动 VPN 网关连接的数据包捕获

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

可选参数 **-FilterData** 可用于应用筛选器。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>在 VPN 网关连接上停止数据包捕获

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>重要注意事项

- 运行数据包捕获可能会影响性能。 不需要数据包捕获时，请记得将其停止。
- 建议的最短数据包捕获持续时间为 600 秒。 由于路径中多个组件之间的同步问题，使用较短的数据包捕获持续时间可能无法提供完整的数据。
- 数据包捕获数据文件以 PCAP 格式生成。 使用 Wireshark 或其他常用应用程序打开 PCAP 文件。

## <a name="next-steps"></a>后续步骤

有关 VPN 网关的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)
