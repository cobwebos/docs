Azure 提供以下 VPN 网关 SKU：

|**SKU**   | S2S/VNet 到 VNet<br>隧道 | P2S<br>连接 | 聚合<br>吞吐量 |
|---       | ---                             | ---                    | ---                         |
|VpnGw1| 最大 30                         | 最大 128               | 500 Mbps                    |
|VpnGw2| 最大 30                         | 最大 128               | 1 Gbps                      |
|VpnGw3| 最大 30                         | 最大 128               | 1.25 Gbps                   |
|**基本** | 最大 10                         | 最大 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- 吞吐量基于对通过单个网关聚合的多个隧道的测量。 受 Internet 流量情况和应用程序行为影响，该吞吐量无法保证。

- 可在 [定价](https://azure.microsoft.com/pricing/details/vpn-gateway) 页上找到定价信息。

- 可在 [SLA](https://azure.microsoft.com/en-us/support/legal/sla/vpn-gateway/) 页查看 SLA（服务级别协议）信息。