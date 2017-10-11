## <a name="azure-dns"></a>Azure DNS
Azure DNS 是 DNS 域的托管服务提供使用 Microsoft Azure 基础结构的名称解析。

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| **DNSzones** |与主机 DNS 记录的特定域的域区域信息 |/ subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com" |

### <a name="dns-record-sets"></a>DNS 记录集
DNS 区域具有名为记录集的子对象。 记录集是按 DNS 区域的类型的主机记录的集合。 记录类型为 A、 AAAA、 CNAME、 MX、 NS、 SOA、 SRV 和 TXT。

| 属性 | 描述 | 示例值 |
| --- | --- | --- |
| A |IPv4 记录类型 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA |IPv6 记录类型 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME |规范名称记录类型<sup>1</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX |邮件记录类型 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS |名称服务器记录类型 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA |开始机构记录类型<sup>2</sup> |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV |服务记录类型 |/subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup>仅允许每个记录集的一个值。

<sup>2</sup>仅允许每个 DNS 区域的一种记录类型 SOA。 

以 Json 格式的 DNS 区域的示例：

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "newZoneName": {
          "type": "String",
          "metadata": {
              "description": "The name of the DNS zone to be created."
          }
        },
        "newRecordName": {
          "type": "String",
          "defaultValue": "www",
          "metadata": {
              "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
          }
        }
      },
      "resources": 
      [
        {
          "type": "microsoft.network/dnszones",
          "name": "[parameters('newZoneName')]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": {
          }
        },
        {
          "type": "microsoft.network/dnszones/a",
          "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": 
          {
            "TTL": 3600,
            "ARecords": 
            [
                {
                    "ipv4Address": "1.2.3.4"
                },
                {
                    "ipv4Address": "1.2.3.5"
                }
            ]
          },
          "dependsOn": [
            "[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
          ]
        }
          ]
    }

## <a name="additional-resources"></a>其他资源
读取[DNS 区域的 REST API 文档](https://msdn.microsoft.com/library/azure/mt130626.aspx)有关详细信息。

读取[DNS 记录集的 REST API 文档](https://msdn.microsoft.com/library/azure/mt130627.aspx)有关详细信息。

