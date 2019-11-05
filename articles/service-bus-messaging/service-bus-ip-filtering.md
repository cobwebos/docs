---
title: Azure 服务总线防火墙规则 | Microsoft Docs
description: 如何使用防火墙规则允许从特定 IP 地址连接到 Azure 服务总线。
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 45415af479c9581ee04b97af4fb5297d09c5769d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496332"
---
# <a name="use-firewall-rules"></a>使用防火墙规则

对于只能从某些已知站点访问 Azure 服务总线的方案，防火墙规则使你能够配置规则，以接受源自特定 IPv4 地址的流量。 例如，这些地址可能是企业 NAT 网关地址。

## <a name="when-to-use"></a>使用时机

如果要安装服务总线，使其仅接收来自指定 IP 地址范围的流量并拒绝其他所有流量，则可以利用“防火墙”来阻止来自其他 IP 地址的服务总线终结点。 例如，结合使用服务总线和 [Azure Express Route][express-route]，以创建到本地基础结构的专用连接。 

## <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

IP 筛选器规则应用于服务总线命名空间级别。 因此，这些规则适用于通过各种受支持协议从客户端发出的所有连接。

如果某 IP 地址与服务总线命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。

## <a name="default-setting"></a>默认设置

门户中服务总线的“IP 筛选器”网格默认为空。 此默认设置表示命名空间接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。

## <a name="ip-filter-rule-evaluation"></a>IP 筛选器规则评估

IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

>[!WARNING]
> 实施防火墙规则可以组织其他 Azure 服务与服务总线进行交互。
>
> 实施 IP 筛选（防火墙规则）时，受信任的 Microsoft 服务不受支持，但很快就会变得可用。
>
> 不适用于 IP 筛选的常见 Azure 方案（请注意，该列表内容并不详尽）-
> - Azure Monitor
> - Azure 流分析
> - 与 Azure 事件网格的集成
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
>
> 以下 Microsoft 服务必须在虚拟网络中
> - Azure 应用服务
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建虚拟网络和防火墙规则

> [!IMPORTANT]
> 防火墙和虚拟网络仅在服务总线的**高级**层中受支持。

以下资源管理器模板支持向现有服务总线命名空间添加虚拟网络规则。

模板参数：

- ipMask 是单个 IPv4 地址或者是以 CIDR 表示法表示的一个 IP 地址块。 例如，在 CIDR 表示法中，70.37.104.0/24 表示从 70.37.104.0 到 70.37.104.255 的 256 个 IPv4 地址，其中 24 表示范围的有效前缀位数。

> [!NOTE]
> 虽然不可能具有拒绝规则，但 Azure 资源管理器模板的默认操作设置为“允许”，不限制连接。
> 制定虚拟网络或防火墙规则时，必须更改“defaultAction”
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> to
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

若要部署模板，请按照 [Azure 资源管理器][lnk-deploy]的说明进行操作。

## <a name="next-steps"></a>后续步骤

若要限制服务总线到 Azure 虚拟网络的访问，请参阅以下链接：

- [服务总线的虚拟网络服务终结点][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
