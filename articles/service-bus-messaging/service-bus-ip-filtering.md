---
title: 配置 Azure 服务总线的 IP 防火墙规则
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
ms.openlocfilehash: 9601689bbce9566b52664058911e9c45647152d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116812"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>配置 Azure 服务总线的 IP 防火墙规则
默认情况下，只要请求附带有效的身份验证和授权，就可以从 internet 访问服务总线命名空间。 使用 IP 防火墙，你可以将其进一步限制为仅一组 IPv4 地址或[CIDR （无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的 ipv4 地址范围。

此功能在 Azure 服务总线应只能从特定的已知站点进行访问的情况下很有用。 防火墙规则使你可以配置规则以接受来自特定 IPv4 地址的流量。 例如，如果你将服务总线与[Azure Express Route][express-route]一起使用，则可以创建**防火墙规则**，以便仅允许来自企业 NAT 网关的本地基础结构 IP 地址或地址的流量。 

> [!IMPORTANT]
> 防火墙和虚拟网络仅在服务总线的**高级**层中受支持。 如果不提供升级到**顶级**层的选项，我们建议你保持共享访问签名（SAS）令牌的安全并与仅授权用户共享。 有关 SAS 身份验证的信息，请参阅[身份验证和授权](service-bus-authentication-and-authorization.md#shared-access-signature)。

## <a name="ip-firewall-rules"></a>IP 防火墙规则
在服务总线命名空间级别应用 IP 防火墙规则。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与服务总线命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

>[!WARNING]
> 实施防火墙规则可以组织其他 Azure 服务与服务总线进行交互。
>
> 实施 IP 筛选（防火墙规则）时，受信任的 Microsoft 服务不受支持，但很快就会变得可用。
>
> 不适用于 IP 筛选的常见 Azure 方案（请注意，该列表内容并不详尽）****-
> - 与 Azure 事件网格的集成
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
>
> 需要在虚拟网络上安装以下 Microsoft 服务
> - Azure 应用服务
> - Azure Functions

## <a name="use-azure-portal"></a>使用 Azure 门户
本部分说明如何使用 Azure 门户为服务总线命名空间创建 IP 防火墙规则。 

1. 在[Azure 门户](https://portal.azure.com)中导航到**服务总线命名空间**。
2. 在左侧菜单中，选择 "**网络**" 选项。 默认情况下，选择 "**所有网络**" 选项。 Service Bus 命名空间接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。 

    !["防火墙-所有网络" 选项已选中](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. 选择页面顶部的 "**所选网络**" 选项。 在 "**防火墙**" 部分中，执行以下步骤：
    1. 选择 "**添加客户端 ip 地址**" 选项，为当前客户端 ip 授予对命名空间的访问权限。 
    2. 对于 "**地址范围**"，请输入特定的 ipv4 地址或 CIDR 表示法中的 ipv4 地址范围。 
    3. 指定是否**允许受信任的 Microsoft 服务跳过此防火墙**。 

        !["防火墙-所有网络" 选项已选中](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. 在工具栏上选择 "**保存**"，保存设置。 等待几分钟让确认显示在门户通知上。

## <a name="use-resource-manager-template"></a>使用 Resource Manager 模板
本部分提供一个示例 Azure 资源管理器模板，用于创建虚拟网络和防火墙规则。


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
