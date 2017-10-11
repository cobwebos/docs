## <a name="nic"></a>NIC
网络接口卡 (NIC) 资源提供到 VNet 资源中现有子网的网络连接。 虽然你可以创建将 NIC 作为独立对象，你需要将其关联到另一个对象才能实际提供连接。 NIC 可以用于将 VM 连接到一个子网、公共 IP 地址或负载均衡器。  

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **virtualMachine** |VM NIC 关联的。 |/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1 |
| **macAddress** |Nic 的 MAC 地址 |4 和 30 之间的任何值 |
| **networkSecurityGroup** |关联的 nic 的 NSG |/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |Nic 的 DNS 设置 |请参阅[PIP](#Public-IP-address) |

网络接口卡或 NIC，表示可与虚拟机 (VM) 相关联的网络接口。 虚拟机可以具有一个或多个 Nic。

![上一个单独的 VM 的 NIC](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>IP 配置
Nic 具有名为的子对象**Ipconfiguration**包含以下属性：

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **子网** |子网的 NIC 是连接。 |/subscriptions/{guid}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1 |
| **privateIPAddress** |子网中 NIC 的 IP 地址 |10.0.0.8 |
| **privateIPAllocationMethod** |IP 分配方法 |动态或静态 |
| **enableIPForwarding** |NIC 是否可以使用用于路由 |True 或 false |
| **主** |NIC 是否是 vm 的主 NIC |True 或 false |
| **publicIPAddress** |与 NIC 关联的 PIP |请参阅[DNS 设置](#DNS-settings) |
| **Loadbalancerbackendaddresspool** |后端地址池与 NIC 关联 | |
| **loadBalancerInboundNatRules** |与 NIC 关联的入站负载均衡器 NAT 规则 | |

以 JSON 格式示例公共 IP 地址：

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>其他资源
* 读取[REST API 参考文档](https://msdn.microsoft.com/library/azure/mt163579.aspx)nic。

