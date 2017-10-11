下表显示网关 SKU 的网关类型和估计的聚合吞吐量。 此表适用于资源管理器和经典部署模型。 

定价不同网关 Sku 之间。 有关详细信息，请参阅[VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway)。

请注意 UltraPerformance 网关 SKU 不表示此表中。 有关 UltraPerformance SKU 的信息，请参阅[ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md)文档。

|  | **VPN 网关吞吐量 (1)** | **VPN 网关最大 IPsec 隧道 (2)** | **ExpressRoute 网关吞吐量** | **VPN 网关和 ExpressRoute 共存** |
| --- | --- | --- | --- | --- |
| **基本 SKU (3)(5)(6)** |100 Mbps |10 |500 Mbps (6) |否 |
| **标准 SKU (4)(5)** |100 Mbps |10 |1000 Mbps |是 |
| **高性能 SKU (4)** |200 Mbps |30 |2000 Mbps |是 |


（1） 的 VPN 吞吐量是粗略的估计基于同一 Azure 区域中的 Vnet 之间的度量值。 它是在 internet 上的不跨界连接有保证的吞吐量。 它是的最大可能的吞吐量测量。

（2） 的隧道数是指 RouteBased Vpn。 PolicyBased VPN 只能支持一个站点到站点 VPN 隧道。

（基本 SKU 不支持 BGP 3)。

（此 sku 不支持 4） 的基于策略的 Vpn。 针对基本 SKU 仅支持它们。

（此 sku 中不支持 5） 主动-主动 S2S VPN 网关连接。 主动-主动 HighPerformance SKU 仅支持。

（6） 基本 SKU 已弃用对与 ExpressRoute 一起使用。
