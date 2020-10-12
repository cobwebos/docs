---
title: 虚拟网络服务终结点 - Azure 事件中心 | Microsoft Docs
description: 本文提供了有关如何将 Microsoft EventHub 服务终结点添加到虚拟网络的信息。
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: cb0d9a9c4d5e2503e68620ec4e6386d8e05d471c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185055"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>允许从特定虚拟网络访问 Azure 事件中心命名空间 

通过将事件中心与[虚拟网络 (VNet) 服务终结点][vnet-sep]相集成，可从绑定到虚拟网络的工作负荷（例如虚拟机）安全地访问消息传递功能，同时在两端保护网络流量路径。

一旦配置为至少绑定到一个虚拟网络子网服务终结点，各自的事件中心命名空间就不再接受来自虚拟网络中的任何位置但获得授权的子网的流量。 从虚拟网络的角度来看，通过将事件中心命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。 

然后，绑定到子网的工作负荷与相应的事件中心命名空间之间将存在专用和独立的关系，消息传递服务终结点的可观察网络地址位于公共 IP 范围内对此没有影响。 此行为有一个例外。 默认情况下，启用服务终结点将启用 `denyall` 与虚拟网络关联的 [IP 防火墙](event-hubs-ip-filtering.md) 中的规则。 可以在 IP 防火墙中添加特定的 IP 地址，以便能够访问事件中心公共终结点。 

>[!IMPORTANT]
> 事件中心的标准**** 和专用**** 层支持虚拟网络。 **基本**层不支持此方法。
>
> 启用事件中心命名空间的防火墙规则会阻止默认情况下的传入请求，除非请求源自从允许的虚拟网络操作的服务。 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。 
>
> 下面是一些在启用虚拟网络时无法访问事件中心资源的服务。 请注意，该列表并未囊括所有方式。
>
> - Azure 流分析
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
> - Azure 事件网格
> - Azure Monitor（诊断设置）
>
> 例外情况是，即使在启用了虚拟网络的情况下，也可以允许从某些受信任的服务访问事件中心资源。 有关受信任服务的列表，请参阅 [受信任服务](#trusted-microsoft-services)。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>通过 VNet 集成启用的高级安全方案 

需要严格和事关的安全，以及虚拟网络子网在事关服务之间提供分段的解决方案，仍需要位于驻留在这些隔离舱中的服务之间的通信路径。

隔离舱之间的任何即时 IP 路由（包括通过 TCP/IP 承载 HTTPS 的）都存在利用网络层漏洞的风险。 消息服务提供了绝缘的通信路径，在这些路径中，消息在参与方之间过渡时甚至写入磁盘。 绑定到同一个事件中心实例的两个不同虚拟网络中的工作负荷可通过消息进行高效和可靠的通信，同时保留各自的网络隔离边界完整性。
 
这意味着安全敏感云解决方案不仅可以访问 Azure 行业领先的可靠且可扩展的异步消息传递功能，而且现在可以使用消息传递在安全解决方案隔离舱之间创建通信路径，这些隔离舱本质上比利用任何对等通信模式（包括 HTTPS 和其他 TLS 安全套接字协议）更加安全。

## <a name="bind-event-hubs-to-virtual-networks"></a>将事件中心绑定到虚拟网络

虚拟网络规则**** 是一种防火墙安全功能，用于控制是否允许 Azure 事件中心命名空间接受来自特定虚拟网络子网的连接。

将事件中心命名空间绑定到虚拟网络的过程分为两步。 首先需要在虚拟网络的子网中创建一个**虚拟网络服务终结点**，并按[服务终结点概述][vnet-sep]一文中所述的那样为其启用 **。** 添加服务终结点后，可以使用 **虚拟网络规则**将事件中心命名空间绑定到该终结点。

虚拟网络规则是事件中心命名空间与虚拟网络子网的关联。 存在此规则时，绑定到子网的所有工作负荷都有权访问事件中心命名空间。 事件中心本身从不建立出站连接，无需获取访问权限，因此永远不会通过启用此规则授予对子网的访问权限。

## <a name="use-azure-portal"></a>使用 Azure 门户
本部分演示如何使用 Azure 门户添加虚拟网络服务终结点。 若要限制访问，需要集成此事件中心命名空间的虚拟网络服务终结点。

1. 在 [Azure 门户](https://portal.azure.com)中导航到“事件中心命名空间”。
4. 在左侧“设置”下选择“网络” 。 只会为“标准”或“专用”命名空间显示“网络”选项卡。 

    > [!NOTE]
    > 默认情况下，“所选网络”选项处于选中状态，如下图所示。 如果未在此页上指定 IP 防火墙规则或添加虚拟网络，则可以通过公共 Internet（使用访问密钥）访问该命名空间。 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="网络选项卡 -“所选网络”选项" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    如果选择“所有网络”选项，则事件中心接受来自任何 IP 地址的连接（使用访问密钥）。 此设置等效于一个接受 0.0.0.0/0 IP 地址范围的规则。 

    ![防火墙 - 选中了“所有网络”选项](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 若要限制对特定网络的访问，请在页面顶部选择 " **所选网络** " 选项（如果尚未选择）。
2. 在页面的 " **虚拟网络** " 部分中，选择 "+ 添加现有虚拟网络"。 如果要创建新的 VNet，请选择 " **+ 创建新虚拟网络** "。 

    ![添加现有虚拟网络](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. 从虚拟网络列表中选择虚拟网络，然后选择“子网”。 将虚拟网络添加到列表之前，必须启用服务终结点。 如果未启用服务终结点，门户将提示启用。
   
   ![选择子网](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. 为 web.config 启用了子网的服务终结点后，应看到以下成功消息 **。** 选择页面底部的“添加”，添加网络。 

    ![选择子网并启用终结点](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 如果无法使用资源管理器模板启用服务终结点，可以忽略有关缺少虚拟网络服务终结点的消息。 此功能在门户中不可用。
5. 指定是否要“允许受信任的 Microsoft 服务绕过此防火墙”。 有关详细信息，请参阅[受信任的 Microsoft 服务](#trusted-microsoft-services)。 
6. 在工具栏上选择“保存”，保存这些设置。 请等待几分钟，直到门户通知中显示确认消息。

    ![保存网络](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > 若要限制对特定 IP 地址或范围的访问，请参阅 [允许从特定 ip 地址或范围进行访问](event-hubs-ip-filtering.md)。

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>使用 Resource Manager 模板

以下资源管理器模板可用于向现有的事件中心命名空间添加虚拟网络规则。

模板参数：

* `namespaceName`：事件中心命名空间。
* `vnetRuleName`：要创建的虚拟网络规则的名称。
* `virtualNetworkingSubnetId`：虚拟网络子网的完全限定资源管理器路径;例如， `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` 对于虚拟网络的默认子网。

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

有关虚拟网络的详细信息，请参阅以下链接：

- [Azure 虚拟网络服务终结点][vnet-sep]
- [Azure 事件中心 IP 筛选][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
