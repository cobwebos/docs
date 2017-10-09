创建虚拟网络网关时，需要指定要使用的网关 SKU。 根据工作负荷、吞吐量、功能和、SLA 的类型，选择满足需求的 SKU。

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>生产与开发-测试工作负荷

由于 SLA 和功能集的差异，建议使用以下 SKU 比较生产与开发-测试：

| **工作负载**                       | SKU               |
| ---                                | ---                    |
| 生产、关键工作负荷 | VpnGw1、VpnGw2、VpnGw3 |
| 开发-测试或概念证明   | 基本                  |
|                                    |                        |

如果使用旧版 SKU，则推荐使用的生产 SKU 为标准和高性能 SKU。 有关老版 SKU 的信息，请参阅[网关 SKU（旧版 SKU）](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)。

###  <a name="feature"></a>网关 SKU 功能集

新版网关 SKU 简化了网关上提供的功能集：

| **SKU**| **功能**|
| ---    | ---         |
|**基本**   | **基于路由的 VPN**：包含 P2S 的 10 个隧道；无 RADIUS 身份验证；无 IKEv2<br>基于策略的 VPN (IKEv1)：1 个隧道；不含 P2S|
| VpnGw1、VpnGw2、VpnGw3 | 基于路由的 VPN：最多 30 个隧道 (*)、P2S、BGP、主动-主动、自定义 IPsec/IKE 策略、ExpressRoute/VPN 共存 |
|        |             |

(*) 可以对“PolicyBasedTrafficSelectors”进行配置，以便将基于路由的 VPN 网关（VpnGw1、VpnGw2、VpnGw3）连接到多个本地的基于策略的防火墙设备。 有关详细信息，请参阅[使用 PowerShell 将 VPN 网关连接到多个本地的基于策略的 VPN 设备](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)。

###  <a name="resize"></a>重设网关 SKU 大小

1. 可以在 VpnGw1 SKU、VpnGw2 SKU 和 VpnGw3 SKU 之间调整大小。
2. 使用旧版网关 SKU 时，仍可在基本、标准和高性能 SKU 之间调整大小。
2. 不能从基本/标准/高性能 SKU 调整为新版 VpnGw1/VpnGw2/VpnGw3 SKU， 而只能[迁移](#migrate)到新版 SKU。

###  <a name="migrate"></a>从旧 SKU 迁移到新 SKU

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
