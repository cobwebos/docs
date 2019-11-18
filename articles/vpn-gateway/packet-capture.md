---
title: Azure VPN 网关：配置数据包捕获
description: 了解可在 VPN 网关上使用的数据包捕获功能。
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 41c36d302605bb619899131a8ace649b0f1439b2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151853"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>为 VPN 网关配置数据包捕获

与连接和性能相关的问题通常都很复杂，只需花费大量时间和精力来缩小问题的原因。 数据包捕获的能力极大地有助于缩短将问题范围缩小到网络特定部分的时间，如问题是在网络的客户端、网络的 Azure 端还是在两者之间的某个位置。 此问题缩小后，调试和采取补救措施要高效得多。

有一些用于数据包捕获的常用工具。 但是，使用这些工具获取相关数据包捕获通常会比使用大容量流量方案时更繁琐。 VPN 网关数据包捕获提供的筛选功能成为了主要优势。 除了常用的数据包捕获工具外，还可以使用 VPN 网关数据包捕获。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN 网关数据包捕获筛选功能

VPN 网关数据包捕获可在网关或特定连接上运行，具体取决于客户需求。 还可以同时在多个隧道上运行数据包捕获。 可以捕获单个或双向流量、IKE 和 ESP 流量以及内部数据包以及 VPN 网关上的筛选。

使用5元组筛选器（源子网、目标子网、源端口、目标端口、协议）和 TCP 标志（SYN、ACK、FIN、URG、PSH、RST）在隔离大量流量的问题时非常有用。

## <a name="setup-packet-capture-using-powershell"></a>使用 PowerShell 安装数据包捕获

请参阅以下示例，了解用于启动和停止数据包捕获的 PowerShell 命令。 有关参数选项（如如何创建筛选器）的详细信息，请参阅此 PowerShell[文档](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>启动 VPN 网关的数据包捕获

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

可选参数 **-FilterData**可用于应用筛选器。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>停止 VPN 网关的数据包捕获

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>启动 VPN 网关连接的数据包捕获

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

可选参数 **-FilterData**可用于应用筛选器。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>在 VPN 网关连接上停止数据包捕获

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>重要注意事项

- 运行数据包捕获可能会影响性能。 请记得在不需要时停止数据包捕获。
- 建议的最小数据包捕获持续时间为600秒。 由于在路径上的多个组件之间同步问题，导致更短的数据包捕获持续时间可能无法提供完整的数据。
- 数据包捕获数据文件以 PCAP 或 ETL 格式生成。 可能需要 Netmon 分析器来理解数据。

## <a name="next-steps"></a>后续步骤

有关 VPN 网关的详细信息，请参阅[关于 Vpn 网关](vpn-gateway-about-vpngateways.md)