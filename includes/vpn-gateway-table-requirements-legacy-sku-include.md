下表列出 PolicyBased 和 RouteBased VPN 网关的要求。 此表适用于资源管理器和经典部署模型。 对于经典模型中，基于策略的 VPN 网关静态网关，相同且基于路由的网关都与动态网关相同。

|  | **PolicyBased Basic VPN 网关** | **RouteBased Basic VPN 网关** | **RouteBased 标准 VPN 网关** | **RouteBased 高性能 VPN 网关** |
| --- | --- | --- | --- | --- |
| **站点到站点连接 (S2S)** |基于策略的 VPN 配置 |RouteBased VPN 配置 |RouteBased VPN 配置 |RouteBased VPN 配置 |
| **点到站点连接 (P2S**) |不支持 |支持 （可与 S2S 共存） |支持 （可与 S2S 共存） |支持 （可与 S2S 共存） |
| **身份验证方法** |预共享的密钥 |预共享的密钥 S2S 连接，P2S 连接的证书 |预共享的密钥 S2S 连接，P2S 连接的证书 |预共享的密钥 S2S 连接，P2S 连接的证书 |
| **最大 S2S 连接数** |1 |10 |10 |30 |
| **最大 P2S 连接数** |不支持 |128 |128 |128 |
| **活动路由支持 (BGP)** |不支持 |不支持 |支持 |支持 |

