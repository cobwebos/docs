## <a name="virtual-network"></a>虚拟网络
虚拟网络 (VNET) 和子网的资源可帮助定义在 Azure 中运行的工作负荷的安全边界。 VNet 的特征是地址空间，定义为 CIDR 块的集合。 

> [!NOTE]
> 网络管理员熟悉 CIDR 表示法。 如果你不熟悉 CIDR，[了解更多相关信息](http://whatismyipaddress.com/cidr)。
> 
> 

![有多个子网的 VNet](./media/resource-groups-networking/Figure4.png)

Vnet 包含以下属性。

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **addressSpace** |构成 CIDR 表示法中的 VNet 的地址前缀的集合 |192.168.0.0/16 |
| **子网** |构成 VNet 的子网的集合 |请参阅[子网](#Subnets)下面。 |
| **ip 地址** |分配给对象的 IP 地址。 这是只读的属性。 |104.42.233.77 |

### <a name="subnets"></a>子网
子网是 VNet 的子资源，可帮助定义在 CIDR 块中，使用 IP 地址前缀的地址空间的段。 Nic 可以是添加到子网，并连接到 Vm，针对各种工作负荷提供连接性。

子网包含以下属性。 

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **addressPrefix** |构成 CIDR 表示法中的子网的单个地址前缀 |192.168.1.0/24 |
| **networkSecurityGroup** |应用到子网的 NSG |请参阅[Nsg](#Network-Security-Group) |
| **routeTable** |应用到子网的路由表 |请参阅[UDR](#Route-table) |
| **Ipconfiguration** |使用连接到子网的 Nic 的 IP configruation 对象的集合 |请参阅[UDR](#Route-table) |

以 JSON 格式的示例 VNet:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>其他资源
* 获取其相关详细信息[VNet](../articles/virtual-network/virtual-networks-overview.md)。
* 读取[REST API 参考文档](https://msdn.microsoft.com/library/azure/mt163650.aspx)vnet。
* 读取[REST API 参考文档](https://msdn.microsoft.com/library/azure/mt163618.aspx)的子网。

