---
title: Azure 事件中心 IP 连接筛选器 | Microsoft Docs
description: 使用 IP 筛选阻止从特定 IP 地址到 Azure 事件中心的连接。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: 0ecce667584f522b5bd6aac28291bda427f37608
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005346"
---
# <a name="use-ip-filters"></a>使用 IP 筛选器

对于只能通过某些已知站点访问 Azure 事件中心的方案，可使用 IP 筛选器功能配置相关规则，以拒绝或接受源自特定 IPv4 地址的流量。 例如，这些地址可能是企业 NAT 网关地址。

## <a name="when-to-use"></a>使用时机

下面是两个重要用例，在要针对特定 IP 地址阻止事件中心终结点接收流量时，这两个用例非常有用：

- 事件中心应仅从指定范围内的 IP 地址接收流量并拒绝任何其他流量。 例如，结合使用事件中心和 [Azure Express Route][express-route]，以创建到本地基础结构的专用连接。 
- 需要拒绝来自事件中心管理员已标识为可疑地址的 IP 地址的流量。

## <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

IP 筛选器规则应用于事件中心命名空间级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。

如果某 IP 地址与事件中心命名空间上的拒绝 IP 规则匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。

## <a name="default-setting"></a>默认设置

默认情况下，门户中针对事件中心的“IP 筛选器”网格为空。 此默认设置意味着事件中心会接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。

## <a name="ip-filter-rule-evaluation"></a>IP 筛选器规则评估

IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

例如，如果希望接受 70.37.104.0/24 范围中的地址并拒绝任何其他地址，则网格中的第一个规则应接受 70.37.104.0/24 地址范围。 下一个规则应通过使用 0.0.0.0/0 范围拒绝所有地址。

> [!NOTE]
> 拒绝 IP 地址即可阻止其他 Azure 服务（例如门户中的 Azure 流分析、Azure 虚拟机或设备资源管理器）与事件中心交互。

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建虚拟网络规则

以下资源管理器模板可用于向现有的事件中心命名空间添加虚拟网络规则。

模板参数：

- ipFilterRuleName 必须是不区分大小写的唯一字母数字字符串，长度不超过 128 个字符。
- ipFilterAction 是要应用到 IP 筛选规则的“拒绝”或“接受”操作。
- ipMask 是单个 IPv4 地址或者是以 CIDR 表示法表示的一个 IP 地址块。 例如，在 CIDR 表示法中，70.37.104.0/24 表示从 70.37.104.0 到 70.37.104.255 的 256 个 IPv4 地址，其中 24 表示范围的有效前缀位数。

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

若要部署模板，请按照 [Azure 资源管理器][lnk-deploy]的说明进行操作。

## <a name="next-steps"></a>后续步骤

若要限制事件中心到 Azure 虚拟网络的访问，请参阅以下链接：

- [事件中心的虚拟网络服务终结点][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md