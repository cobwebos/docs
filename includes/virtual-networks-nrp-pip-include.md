## <a name="public-ip-address"></a>公共 IP 地址
公共 IP 地址资源提供面向 IP 地址保留或动态 Internet。 虽然你可以创建作为独立对象的公共 IP 地址，你需要将其关联到另一个对象才能实际使用该地址。 你可以关联到负载平衡器、 应用程序网关或 NIC 以提供对这些资源的 Internet 访问的公共 IP 地址。  

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **publicIPAllocationMethod** |定义 IP 地址是否*静态*或*动态*。 |static、 dynamic |
| **idleTimeoutInMinutes** |定义空闲超时默认值为 4 分钟。 如果在此时间内收到指定会话没有多个数据包时，被终止该会话。 |4 和 30 之间的任何值 |
| **ip 地址** |分配给对象的 IP 地址。 这是只读的属性。 |104.42.233.77 |

### <a name="dns-settings"></a>DNS 设置
公共 IP 地址具有名为的子对象**dnsSettings**包含以下属性：

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **domainNameLabel** |主机名为用于名称解析。 |www、 ftp、 vm1 |
| **fqdn** |公共 ip 的完全限定的名称。 |www.westus.cloudapp.azure.com |
| **reverseFqdn** |完全限定的域名解析为 IP 地址并为 PTR 记录的 DNS 中注册的名称。 |www.contoso.com。 |

以 JSON 格式示例公共 IP 地址：

    {
       "name": "PIP01",
       "location": "North US",
       "tags": { "key": "value" },
       "properties": {
          "publicIPAllocationMethod": "Static",
          "idleTimeoutInMinutes": 4,
          "ipAddress": "104.42.233.77",
          "dnsSettings": {
             "domainNameLabel": "mylabel",
             "fqdn": "mylabel.westus.cloudapp.azure.com",
             "reverseFqdn": "contoso.com."
          }
       }
    } 

### <a name="additional-resources"></a>其他资源
* 获取其相关详细信息[公共 IP 地址](../articles/virtual-network/virtual-networks-reserved-public-ip.md)。
* 了解有关[实例级公共 IP 地址](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)。
* 读取[REST API 参考文档](https://msdn.microsoft.com/library/azure/mt163638.aspx)公有 ip 地址。

