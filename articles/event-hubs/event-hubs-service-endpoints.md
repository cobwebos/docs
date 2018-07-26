---
title: 适用于 Azure 事件中心的虚拟网络服务终结点和规则 | Microsoft Docs
description: 将 Microsoft.EventHub 服务终结点添加到虚拟网络。
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: 3746c4b7d1b53d7522f317fd2e349d31ba77f406
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136325"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>将虚拟网络服务终结点与 Azure 事件中心配合使用

通过将事件中心与[虚拟网络 (VNet) 服务终结点][vnet-sep]相集成，可从绑定到虚拟网络的工作负荷（例如虚拟机）安全地访问消息传递功能，同时在两端保护网络流量路径。 

配置为绑定到至少一个虚拟网络子网服务终结点后，相应的事件中心命名空间将不再接受来自经授权的虚拟网络以外的任何位置的流量。 从虚拟网络的角度来看，通过将事件中心命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。

然后，绑定到子网的工作负荷与相应的事件中心命名空间之间将存在专用和独立的关系，消息传递服务终结点的可观察网络地址位于公共 IP 范围内对此没有影响。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>通过 VNet 集成启用的高级安全方案 

对于需要严格和隔离安全性的解决方案和虚拟网络子网在其中的隔离服务之间提供分段的解决方案，它们通常仍然需要驻留在这些隔离舱中的服务之间的通信路径。

隔离舱之间的任何即时 IP 路由（包括通过 TCP/IP 承载 HTTPS 的）都存在利用网络层漏洞的风险。 消息传递服务提供完全隔离的通信路径，其中消息在各方之间转换时会以平均方式写入磁盘。 绑定到同一个事件中心实例的两个不同虚拟网络中的工作负荷可通过消息进行高效和可靠的通信，同时保留各自的网络隔离边界完整性。
 
这意味着安全敏感云解决方案不仅可以访问 Azure 行业领先的可靠且可扩展的异步消息传递功能，而且现在可以使用消息传递在安全解决方案隔离舱之间创建通信路径，这些隔离舱本质上比利用任何对等通信模式（包括 HTTPS 和其他 TLS 安全套接字协议）更加安全。

## <a name="bind-event-hubs-to-virtual-networks"></a>将事件中心绑定到虚拟网络

虚拟网络规则是一种防火墙安全功能，用于控制是否允许 Azure 事件中心服务器接受来自特定虚拟网络子网的连接。

将事件中心命名空间绑定到虚拟网络的过程分为两步。 首先需要在虚拟网络子网上创建“虚拟网络服务终结点”，并按照[服务终结点概述][vnet-sep]中的说明为“Microsoft.EventHub”启用该终结点。 添加服务终结点后，使用虚拟网络规则将事件中心命名空间绑定到该终结点。

虚拟网络规则是事件中心命名空间与虚拟网络子网的命名关联。 存在此规则时，绑定到子网的所有工作负荷都有权访问事件中心命名空间。 事件中心本身永远不会建立出站连接，不需要获得访问权限，因此永远不会通过启用此规则来授予对子网的访问权限。

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建虚拟网络规则

以下资源管理器模板可用于向现有的事件中心命名空间添加虚拟网络规则。

模板参数：

* **namespaceName**：事件中心命名空间。
* **vnetRuleName**：要创建的虚拟网络规则的名称。
* **virtualNetworkingSubnetId**：虚拟网络子网的完全限定的资源管理器路径；例如，虚拟网络默认子网的 `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default`。

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
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.EventHub/namespaces/VirtualNetworkRules",         
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

若要部署模板，请按照 [Azure 资源管理器][lnk-deploy]的说明进行操作。

## <a name="next-steps"></a>后续步骤

有关虚拟网络的详细信息，请参阅以下链接：

- [Azure 虚拟网络服务终结点][vnet-sep]
- [Azure 事件中心 IP 筛选][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md