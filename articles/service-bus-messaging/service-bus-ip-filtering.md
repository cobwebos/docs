---
title: 为 Azure 服务总线配置 IP 防火墙规则
description: 如何使用防火墙规则允许从特定 IP 地址连接到 Azure 服务总线。
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: a20882de34cb306b767959e21327180ff284e658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475937"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>为 Azure 服务总线配置 IP 防火墙规则
默认情况下，只要请求附带有效的身份验证和授权，服务总线命名空间即可从 Internet 访问。 使用 IP 防火墙，您可以进一步将其限制为[CIDR（无类域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的一组 IPv4 地址或 IPv4 地址范围。

此功能在 Azure 服务总线应仅从某些已知站点访问的情况下非常有用。 防火墙规则使您能够配置规则以接受来自特定 IPv4 地址的流量。 例如，如果将服务总线与 Azure[快速路由][express-route]一起使用 ，则可以创建**防火墙规则**，仅允许来自本地基础结构 IP 地址或公司 NAT 网关地址的流量。 

## <a name="ip-firewall-rules"></a>IP 防火墙规则
IP 防火墙规则在服务总线命名空间级别应用。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与服务总线命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

## <a name="use-azure-portal"></a>使用 Azure 门户
本节介绍如何使用 Azure 门户为服务总线命名空间创建 IP 防火墙规则。 

1. 导航到[Azure 门户](https://portal.azure.com)中的**服务总线命名空间**。
2. 在左侧菜单上，选择 **"网络**"选项。 默认情况下，选择"**所有网络**"选项。 服务总线命名空间接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。 

    ![防火墙 - 选择的所有网络选项](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. 选择页面顶部的 **"选定网络**"选项。 在 **"防火墙"** 部分中，按照以下步骤操作：
    1. 选择 **"添加客户端 IP 地址**"选项，使当前客户端 IP 有权访问命名空间。 
    2. 对于**地址范围**，在 CIDR 表示法中输入特定的 IPv4 地址或 IPv4 地址范围。 
    3. 指定是否要**允许受信任的 Microsoft 服务绕过此防火墙**。 

        ![防火墙 - 选择的所有网络选项](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. 选择 **"在**工具栏上保存"以保存设置。 等待几分钟，确认显示在门户通知上。

## <a name="use-resource-manager-template"></a>使用 Resource Manager 模板
本节具有创建虚拟网络和防火墙规则的示例 Azure 资源管理器模板。

> [!IMPORTANT]
> 防火墙和虚拟网络仅在服务总线的**高级**层中受支持。

以下资源管理器模板支持向现有服务总线命名空间添加虚拟网络规则。

模板参数：

- ipMask 是单个 IPv4 地址或者是以 CIDR 表示法表示的一个 IP 地址块****。 例如，在 CIDR 表示法中，70.37.104.0/24 表示从 70.37.104.0 到 70.37.104.255 的 256 个 IPv4 地址，其中 24 表示范围的有效前缀位数。

> [!NOTE]
> 虽然不可能具有拒绝规则，但 Azure 资源管理器模板的默认操作设置为“允许”，不限制连接****。
> 制定虚拟网络或防火墙规则时，必须更改“defaultAction”******
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
          "trustedServiceAccessEnabled": false,          
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

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
