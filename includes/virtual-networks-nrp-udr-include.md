## <a name="route-tables"></a>路由表
路由表资源包含用于定义你的 Azure 基础结构内流量的流动方式的路由。 你可以使用用户定义的路由 (UDR) 将从指定的子网的所有流量发送到虚拟设备，如防火墙或入侵检测系统 (IDS)。 你可以关联到子网的路由表。 

路由表包含以下属性。

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **路由** |集合的用户定义的路由表中的路由 |请参阅[用户定义的路由](#User-defined-routes) |
| **子网** |子网的路由表应用于集合 |请参阅[子网](#Subnets) |

### <a name="user-defined-routes"></a>用户定义的路由
你可以创建 Udr，若要指定应将流量发送到，基于其目标地址。 可以路由看作基于网络数据包的目标地址的默认网关定义。

Udr 包含以下属性。 

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **addressPrefix** |地址前缀或为目标的完整 IP 地址 |192.168.1.0/24, 192.168.1.101 |
| **nextHopType** |类型的流量将发送到的设备 |VirtualAppliance，VPN 网关，Internet |
| **nextHopIpAddress** |下一个跃点的 IP 地址 |192.168.1.4 |

以 JSON 格式的示例路由表：

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### <a name="additional-resources"></a>其他资源
* 获取其相关详细信息[Udr](../articles/virtual-network/virtual-networks-udr-overview.md)。
* 读取[REST API 参考文档](https://msdn.microsoft.com/library/azure/mt502549.aspx)路由表。
* 读取[REST API 参考文档](https://msdn.microsoft.com/library/azure/mt502539.aspx)用户定义的路由 (Udr)。

