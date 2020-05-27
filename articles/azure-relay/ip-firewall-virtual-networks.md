---
title: 为 Azure 中继命名空间配置 IP 防火墙
description: 本文介绍如何使用防火墙规则来允许从特定 IP 地址到 Azure 中继命名空间的连接。
services: service-bus-relay
documentationcenter: ''
author: spelluru
ms.service: service-bus-relay
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 05/13/2020
ms.author: spelluru
ms.openlocfilehash: 88eb7acf1e72084a83d6d8631c0ea5d740988640
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653421"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>为 Azure 中继命名空间配置 IP 防火墙
默认情况下，只要请求附带有效的身份验证和授权，就可以从 Internet 访问中继命名空间。 有了 IP 防火墙，就可以使用 [CIDR（无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法将其进一步限制为仅一组 IPv4 地址或 IPv4 地址范围。

在仅应从某些知名站点访问 Azure 中继的情况下，此功能很有用。 可以通过防火墙规则来配置规则，以便接受来自特定 IPv4 地址的流量。 例如，如果将中继与 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) 配合使用，则可创建防火墙规则，仅允许来自本地基础结构 IP 地址的流量。 


> [!IMPORTANT]
> 此功能目前处于预览状态。 


## <a name="enable-ip-firewall-rules"></a>启用 IP 防火墙规则
在命名空间级别应用 IP 防火墙规则。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与命名空间的允许 IP 规则不匹配，系统会拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

### <a name="use-azure-portal"></a>使用 Azure 门户
本部分演示如何使用 Azure 门户为命名空间创建 IP 防火墙规则。 

1. 在 [Azure 门户](https://portal.azure.com)中导航到“中继命名空间”。
2. 在左侧菜单中，选择“网络”选项。 如果在“允许的访问来源”部分中选择“所有网络”选项，则中继命名空间会接受来自任何 IP 地址的连接。 此设置等效于一个接受 0.0.0.0/0 IP 地址范围的规则。 

    ![防火墙 - 已选择“所有网络”选项](./media/ip-firewall/all-networks-selected.png)
1. 若要将访问限制为特定网络和 IP 地址，请选择“所选网络”选项。 在“防火墙”部分中执行以下步骤：
    1. 选择“添加客户端 IP 地址”选项，使当前客户端 IP 可以访问命名空间。 
    2. 对于“地址范围”，请输入某个特定的 IPv4 地址或以 CIDR 表示法表示的 IPv4 地址范围。 
    3. 指定是否要“允许受信任的 Microsoft 服务绕过此防火墙”。 

        ![防火墙 - 已选择“所有网络”选项](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. 选择工具栏上的“保存”以保存设置。 请等待几分钟，直到门户通知中显示确认消息。


### <a name="use-resource-manager-template"></a>使用 Resource Manager 模板
以下资源管理器模板可用于向现有的中继命名空间添加 IP 筛选器规则。

模板采用一个参数：ipMask，它是单个 IPv4 地址或以 CIDR 表示法表示的一个 IP 地址块。 例如，在 CIDR 表示法中，70.37.104.0/24 表示从 70.37.104.0 到 70.37.104.255 的 256 个 IPv4 地址，其中 24 表示范围的有效前缀位数。

> [!NOTE]
> 虽然不可能具有拒绝规则，但 Azure 资源管理器模板的默认操作设置为“允许”，不限制连接。
> 制定虚拟网络或防火墙规则时，必须更改“defaultAction”
> 
> 从
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
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
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
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

若要部署模板，请按照 [Azure 资源管理器](../azure-resource-manager/templates/deploy-powershell.md)的说明进行操作。



## <a name="next-steps"></a>后续步骤
若要了解其他与网络安全性相关的功能，请参阅[网络安全性](network-security.md)。


<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
