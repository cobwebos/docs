## <a name="traffic-manager-profile"></a>Traffic Manager 配置文件
流量管理器和其子终结点资源启用 DNS 路由到在 Azure 中和在 Azure 外部的终结点。 此类流量分配受路由策略方法。 重定向流量根据终结点的运行状况和流量管理器还允许要监视的终结点运行状况。 

| 属性 | 描述 |
| --- | --- |
| **trafficRoutingMethod** |可能的值为*性能*，*加权*，和*优先级* |
| **dnsConfig** |配置文件的 FQDN |
| **协议** |监视协议，可能的值为*HTTP*和*HTTPS* |
| **端口** |端口监视 |
| **路径** |监视路径 |
| **终结点** |终结点资源的容器 |

### <a name="endpoint"></a>终结点
终结点是流量管理器配置文件的子资源。 它表示服务或 web 终结点流量分配到哪些用户基于 Traffic Manager 配置文件资源中配置的策略。 

| 属性 | 描述 |
| --- | --- |
| **类型** |终结点的类型，可能的值为*Azure 终结点*，*外部终结点*，和*嵌套终结点* |
| **targetResourceId** |服务或 web 终结点的公共 IP 地址。 这可以是 Azure 或外部终结点。 |
| **权重** |流量管理中使用的终结点权重。 |
| **优先级** |用于定义故障转移操作的终结点的优先级 |

以 Json 格式的示例的流量管理器中： 

        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }


## <a name="additional-resources"></a>其他资源
读取[流量管理器 REST API 文档](https://msdn.microsoft.com/library/azure/mt163664.aspx)有关详细信息。

