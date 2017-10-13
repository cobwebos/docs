## <a name="route-tables"></a>路由表
路由表资源包含的路由用于定义流量在 Azure 基础结构中的流动方式。 可以使用用户定义的路由 (UDR) 将所有流量从给定子网发送到某个虚拟设备，例如防火墙或入侵检测系统 (IDS)。 可以将路由表关联到子网。 

路由表包含以下属性。

| 属性 | 说明 | 示例值 |
| --- | --- | --- |
| **routes** |路由表中用户定义的路由的集合 |请参阅[用户定义路由](#User-defined-routes) |
| **subnets** |路由表所适用的子网的集合 |请参阅[子网](#Subnets) |

### <a name="user-defined-routes"></a>用户定义的路由
可以创建 UDR，指定应将流量发送到何处，具体取决于其目标地址。 可以将路由视为默认的网关定义（基于网络数据包的目标地址）。

UDR 包含以下属性。 

| 属性 | 说明 | 示例值 |
| --- | --- | --- |
| **addressPrefix** |地址前缀或目标的完整 IP 地址 |192.168.1.0/24, 192.168.1.101 |
| **nextHopType** |要向其发送流量的设备的类型 |VirtualAppliance、VPN 网关、Internet |
| **nextHopIpAddress** |下一个跃点的 IP 地址 |192.168.1.4 |

JSON 格式的示例路由表：

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
* 获取有关 [UDR](../articles/virtual-network/virtual-networks-udr-overview.md) 的详细信息。
* 阅读路由表的 [REST API 参考文档](https://msdn.microsoft.com/library/azure/mt502549.aspx)。
* 阅读用户定义路由 (UDR) 的 [REST API 参考文档](https://msdn.microsoft.com/library/azure/mt502539.aspx)。

