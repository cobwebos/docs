---
title: Azure 事件中心防火墙规则 | Microsoft Docs
description: 使用防火墙规则允许从特定 IP 地址到 Azure 事件中心的链接。
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: ccb2fa7b0805b332957513c52c0c1051d068d2cc
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507529"
---
# <a name="use-firewall-rules"></a>使用防火墙规则

对于只应从某些已知站点访问 Azure 事件中心的方案，可使用防火墙规则配置相关规则，以接受源自特定 IPv4 地址的流量。 例如，这些地址可能是企业 NAT 网关地址。

## <a name="when-to-use"></a>使用时机

如果要设置事件中心命名空间，使其仅接收来自指定 IP 地址范围的流量并拒绝其他所有流量，则可以利用“防火墙规则”来阻止来自其他 IP 地址的事件中心终结点。 例如，如果将事件中心与 [Azure 快速路由][express-route]结合使用，则可以创建防火墙规则来限制来自本地基础结构 IP地址的流量。

## <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

IP 筛选器规则应用于事件中心命名空间级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。

如果某 IP 地址与事件中心命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。

## <a name="default-setting"></a>默认设置

默认情况下，门户中针对事件中心的“IP 筛选器”网格为空。 此默认设置意味着事件中心会接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。

## <a name="ip-filter-rule-evaluation"></a>IP 筛选器规则评估

IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

>[!WARNING]
> 实现防火墙可以阻止其他 Azure 服务与事件中心进行交互。
>
> 实施 IP 筛选（防火墙）时，受信任的 Microsoft 服务不受支持，但很快就会变得可用。
>
> 不适用于 IP 筛选的常见 Azure 方案（请注意，该列表内容并不详尽）-
> - Azure Monitor
> - Azure 流分析
> - 与 Azure 事件网格的集成
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
> - Azure 数据资源管理器
>
> 以下 Microsoft 服务必须在虚拟网络中
> - Azure Web 应用
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建防火墙规则

> [!IMPORTANT]
> 事件中心的标准层和专用层支持防火墙规则。 基本层不支持它。

以下资源管理器模板可用于向现有的事件中心命名空间添加 IP 筛选器规则。

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
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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

若要限制事件中心到 Azure 虚拟网络的访问，请参阅以下链接：

- [事件中心的虚拟网络服务终结点][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
