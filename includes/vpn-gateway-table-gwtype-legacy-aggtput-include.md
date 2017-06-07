下表按网关 SKU 显示了网关类型和估计的聚合吞吐量。 此表适用于 Resource Manager 与经典部署模型。 价格根据网关 SKU 的不同而异。 有关详细信息，请参阅 [VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway)。

请注意，此表中未显示 UltraPerformance 网关 SKU。 有关 UltraPerformance SKU 的信息，请参阅 [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) 文档。

|  | **VPN 网关吞吐量 (1)** | **VPN 网关最大 IPsec 隧道数 (2)** | **ExpressRoute 网关吞吐量** | **VPN 网关和 ExpressRoute 共存** |
| --- | --- | --- | --- | --- |
| **基本 SKU (3)(5)(6)** |100 Mbps |10 |500 Mbps (6) |否 |
| **标准 SKU (4)(5)** |100 Mbps |10 |1000 Mbps |是 |
| **高性能 SKU (4)** |200 Mbps |30 |2000 Mbps |是 |

* (1) VPN 吞吐量是根据同一 Azure 区域 VNet 之间的度量进行的粗略估计。 不能保证该吞吐量通过 Internet 跨界连接。 它是可能的最大吞吐量。
* (2) 隧道数与 RouteBased VPN 相关。 PolicyBased VPN 只能支持一个站点到站点 VPN 隧道。
* (3) 基本 SKU 不支持 BGP。
* (4) 此 SKU 不支持 PolicyBased VPN。 仅基本 SKU 支持它们。
* (5) 此 SKU 不支持主动-主动 S2S VPN 网关连接。 只有 HighPerformance SKU 才支持主动-主动连接。
* (6) 用于 Expressroute 的基本 SKU 已弃用。
