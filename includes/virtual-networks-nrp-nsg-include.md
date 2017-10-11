## <a name="network-security-group"></a>网络安全组
使用 NSG 资源可以创建工作负荷的安全边界，通过实现允许和拒绝规则。 此类规则可以应用于虚拟机、 NIC 或子网。

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **子网** |NSG 应用于子网 id 的列表。 |/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd |
| **securityRules** |构成 NSG 的安全规则的列表 |请参阅[安全规则](#Security-rule)下面 |
| **defaultSecurityRules** |每个 NSG 中出现的默认安全规则的列表 |请参阅[默认安全规则](#Default-security-rules)下面 |

* **安全规则**-一个 NSG 可以有多个定义的安全规则。 每个规则可以允许或拒绝不同类型的流量。

### <a name="security-rule"></a>安全规则
安全规则是 NSG 包含以下属性的子资源。

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **说明** |规则说明 |允许的子网 X 中的所有虚拟机的入站的流量 |
| **协议** |协议以匹配规则 |TCP、 UDP 或 * |
| **sourcePortRange** |源端口范围，以匹配规则 |80, 100-200, * |
| **destinationPortRange** |目标端口范围，以匹配规则 |80, 100-200, * |
| **sourceAddressPrefix** |源地址前缀，以匹配规则 |10.10.10.1，10.10.10.0/24，VirtualNetwork |
| **destinationAddressPrefix** |目标地址前缀，以匹配规则 |10.10.10.1，10.10.10.0/24，VirtualNetwork |
| **方向** |若要匹配规则的流量方向 |入站或出站 |
| **优先级** |规则的优先级。 检查规则按优先顺序规则应用后, 不再对规则进行测试以匹配。 |10, 100, 65000 |
| **访问** |当与匹配的规则要应用的访问类型 |允许或拒绝 |

以 JSON 格式的示例 NSG 中：

    {
        "name": "NSG-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkSecurityGroups",
        "location": "westus",
        "tags": {
            "displayName": "NSG - Front End"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "securityRules": [
                {
                    "name": "rdp-rule",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "description": "Allow RDP",
                        "protocol": "Tcp",
                        "sourcePortRange": "*",
                        "destinationPortRange": "3389",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "access": "Allow",
                        "priority": 100,
                        "direction": "Inbound"
                    }
                }
            ],
            "defaultSecurityRules": [
                { [...],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                }
            ]
        }
    }

### <a name="default-security-rules"></a>默认安全规则

默认安全规则具有相同的属性安全规则中可用。 它们的存在为了提供有 Nsg 应用于它们的资源之间的基本连接。 请确保你知道哪些[默认安全规则](../articles/virtual-network/virtual-networks-nsg.md#default-rules)存在。

### <a name="additional-resources"></a>其他资源
* 获取其相关详细信息[Nsg](../articles/virtual-network/virtual-networks-nsg.md)。
* 读取[REST API 参考文档](https://msdn.microsoft.com/library/azure/mt163615.aspx)Nsg 的。
* 读取[REST API 参考文档](https://msdn.microsoft.com/library/azure/mt163580.aspx)安全规则。
