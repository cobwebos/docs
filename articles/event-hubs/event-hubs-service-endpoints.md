---
title: 虚拟网络服务终结点 - Azure 事件中心 | Microsoft Docs
description: 本文提供有关如何向虚拟网络添加 Microsoft.EventHub 服务终结点的信息。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 91b08d6130da640adc28a3b7d85bd33f0e876caf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390287"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>将虚拟网络服务终结点与 Azure 事件中心配合使用

通过将事件中心与[虚拟网络 (VNet) 服务终结点][vnet-sep]相集成，可从绑定到虚拟网络的工作负荷（例如虚拟机）安全地访问消息传递功能，同时在两端保护网络流量路径。

一旦配置为至少绑定到一个虚拟网络子网服务终结点，相应的事件中心命名空间将不再接受来自虚拟网络中的经过授权的子网的流量。 从虚拟网络的角度来看，通过将事件中心命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。 

然后，绑定到子网的工作负荷与相应的事件中心命名空间之间将存在专用和独立的关系，消息传递服务终结点的可观察网络地址位于公共 IP 范围内对此没有影响。 此行为有一个例外。 默认情况下，启用服务终结点可在[IP](event-hubs-ip-filtering.md)防火墙`denyall`中启用与虚拟网络关联的规则。 您可以在 IP 防火墙中添加特定的 IP 地址，以启用对事件中心公共终结点的访问。 

>[!WARNING]
> 实现虚拟网络集成可以防止其他 Azure 服务与事件中心进行交互。
>
> 实现虚拟网络时，受信任的 Microsoft 服务不受支持。
>
> 不适用于虚拟网络常见 Azure 方案（请注意，该列表内容并不详尽）****-
> - Azure 监视器（诊断设置）
> - Azure 流分析
> - 与 Azure 事件网格的集成
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
>
> 以下 Microsoft 服务需要位于虚拟网络上
> - Azure Web 应用
> - Azure Functions


> [!IMPORTANT]
> 事件中心的标准**** 和专用**** 层支持虚拟网络。 **基本**层不支持它。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>通过 VNet 集成启用的高级安全方案 

需要严格和分门别类安全性的解决方案，以及虚拟网络子网在隔离服务之间提供分段时，仍然需要位于这些隔离区中的服务之间的通信路径。

隔离舱之间的任何即时 IP 路由（包括通过 TCP/IP 承载 HTTPS 的）都存在利用网络层漏洞的风险。 消息传递服务提供绝缘通信路径，其中消息甚至在各方之间转换时写入磁盘。 绑定到同一个事件中心实例的两个不同虚拟网络中的工作负荷可通过消息进行高效和可靠的通信，同时保留各自的网络隔离边界完整性。
 
这意味着安全敏感云解决方案不仅可以访问 Azure 行业领先的可靠且可扩展的异步消息传递功能，而且现在可以使用消息传递在安全解决方案隔离舱之间创建通信路径，这些隔离舱本质上比利用任何对等通信模式（包括 HTTPS 和其他 TLS 安全套接字协议）更加安全。

## <a name="bind-event-hubs-to-virtual-networks"></a>将事件中心绑定到虚拟网络

虚拟网络规则**** 是一种防火墙安全功能，用于控制是否允许 Azure 事件中心命名空间接受来自特定虚拟网络子网的连接。

将事件中心命名空间绑定到虚拟网络的过程分为两步。 首先需要在虚拟网络的子网上创建**虚拟网络服务终结点**，并为**Microsoft.EventHub**启用它，如[服务终结点概述][vnet-sep]文章中所述。 添加服务终结点后，使用虚拟网络规则将事件中心命名空间绑定到该终结点****。

虚拟网络规则是事件中心命名空间与虚拟网络子网的关联。 存在此规则时，绑定到子网的所有工作负荷都有权访问事件中心命名空间。 事件中心本身永远不会建立出站连接，不需要访问，因此永远不会通过启用此规则被授予对子网的访问权限。

## <a name="use-azure-portal"></a>使用 Azure 门户
本节介绍如何使用 Azure 门户添加虚拟网络服务终结点。 要限制访问，您需要集成此事件中心命名空间的虚拟网络服务终结点。

1. 导航到[Azure 门户](https://portal.azure.com)中的**事件中心命名空间**。
2. 在左侧菜单上，选择 **"网络**"选项。 如果选择"**所有网络"** 选项，事件中心将接受来自任何 IP 地址的连接。 此设置等效于接受 0.0.0.0/0 IP 地址范围的规则。 

    ![防火墙 - 选择的所有网络选项](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 要恢复对特定网络的访问，请选择页面顶部的 **"选定网络"** 选项。
2. 在页面的 **"虚拟网络**"部分中，选择[添加现有虚拟网络]。 如果要创建新的 VNet，请选择 **"创建新虚拟网络**"。 

    ![添加现有虚拟网络](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. 从虚拟网络列表中选择虚拟网络，然后选择**子网**。 在将虚拟网络添加到列表中之前，必须启用服务终结点。 如果未启用服务终结点，门户将提示您启用它。
   
   ![选择子网](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. 在为**Microsoft**启用子网的服务终结点后，您应该会看到以下成功消息。 选择**页面底部的"添加**"以添加网络。 

    ![选择子网并启用终结点](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 如果无法启用服务终结点，则可以使用资源管理器模板忽略缺少的虚拟网络服务终结点。 此功能在门户中不可用。
6. 选择 **"在**工具栏上保存"以保存设置。 等待几分钟，确认显示在门户通知上。

    ![保存网络](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>使用 Resource Manager 模板

以下资源管理器模板可用于向现有的事件中心命名空间添加虚拟网络规则。

模板参数：

* **namespaceName**：事件中心命名空间。
* **vnetRuleName**：要创建的虚拟网络规则的名称。
* **virtualNetworkingSubnetId**：虚拟网络子网的完全限定的资源管理器路径；例如，虚拟网络默认子网的 `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default`。

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
