---
title: Azure 中继命名空间的后 IP 防火墙
description: 本文介绍如何使用防火墙规则来允许从特定 IP 地址到 Azure 中继命名空间的连接。
services: service-bus-relay
documentationcenter: ''
author: spelluru
ms.service: service-bus-relay
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: e2a9e89ca8c3b77b09a58a45959f1acda1457b56
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211049"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>为 Azure 中继命名空间配置 IP 防火墙
默认情况下，只要请求附带有效的身份验证和授权，就可以从 internet 访问中继命名空间。 使用 IP 防火墙，你可以将其进一步限制为仅一组 IPv4 地址或[CIDR （无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的 ipv4 地址范围。

此功能在以下情况下非常有用：应只能从特定的已知站点访问 Azure 中继。 可使用防火墙规则配置相关规则，以接受源自特定 IPv4 地址的流量。 例如，如果将中继与[Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)结合使用，则可以创建**防火墙规则**，以便仅允许来自本地基础结构 IP 地址的流量。 


> [!IMPORTANT]
> 此功能目前以预览版提供。 


## <a name="enable-ip-firewall-rules"></a>启用 IP 防火墙规则
在命名空间级别应用 IP 防火墙规则。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 与命名空间上的允许的 IP 规则不匹配的 IP 地址的任何连接尝试被拒绝为未授权。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

### <a name="use-azure-portal"></a>使用 Azure 门户
本部分演示如何使用 Azure 门户为命名空间创建 IP 防火墙规则。 

1. 在[Azure 门户](https://portal.azure.com)中导航到**中继命名空间**。
2. 在左侧菜单中选择“网络”选项。  如果在 "**允许访问**" 部分中选择 "**所有网络**" 选项，则中继命名空间接受来自任何 IP 地址的连接。 此设置等效于一项接受 0.0.0.0/0 IP 地址范围的规则。 

    ![防火墙 - 选中了“所有网络”选项](./media/ip-firewall/all-networks-selected.png)
1. 若要将访问范围限制为特定的网络和 IP 地址，请选择“所选网络”  选项。 在“防火墙”部分执行以下步骤： 
    1. 选择“添加客户端 IP 地址”  选项，为当前客户端 IP 授予对命名空间的访问权限。 
    2. 对于“地址范围”  ，请输入采用 CIDR 表示法的特定 IPv4 地址或 IPv4 地址范围。 
    3. 指定是否想要“允许受信任的 Microsoft 服务跳过此防火墙”。  

        ![防火墙 - 选中了“所有网络”选项](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. 在工具栏上选择“保存”  ，保存这些设置。 等待几分钟，直到门户通知中显示确认消息。


### <a name="use-resource-manager-template"></a>使用 Resource Manager 模板
以下资源管理器模板允许向现有中继命名空间添加 IP 筛选器规则。

模板采用一个参数： **ipMask**，它是一个 IPv4 地址或 CIDR 表示法中的 IP 地址块。 例如，在 CIDR 表示法中，70.37.104.0/24 表示从 70.37.104.0 到 70.37.104.255 的 256 个 IPv4 地址，其中 24 表示范围的有效前缀位数。

> [!NOTE]
> 虽然不可能具有拒绝规则，但 Azure 资源管理器模板的默认操作设置为“允许”，不限制连接  。
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
若要了解其他与网络安全相关的功能，请参阅[网络安全](network-security.md)。


<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[.lnk-deploy]：: 
