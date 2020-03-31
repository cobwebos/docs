---
title: Azure 虚拟网络服务终结点策略 | Microsoft Docs
description: 了解如何使用服务终结点策略来筛选发往 Azure 服务资源的虚拟网络流量
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651282"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>用于 Azure 存储的虚拟网络服务终结点策略

虚拟网络 （VNet） 服务终结点策略允许您通过服务终结点筛选到 Azure 存储帐户的虚拟网络流量，并允许仅将数据渗出到特定的 Azure 存储帐户。 通过服务终结点连接时，终结点策略为虚拟网络流量提供对 Azure 存储的精细访问控制。

![保护到 Azure 存储帐户的虚拟网络出站流量](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

此功能通常可用于__所有全局 Azure 区域__中的__Azure 存储__。

## <a name="key-benefits"></a>主要优点

虚拟网络服务终结点策略提供以下优势：

- __提高虚拟网络流量到 Azure 存储的安全性__

  [用于网络安全组的 Azure 服务标记](https://aka.ms/servicetags)允许您将虚拟网络出站流量限制为特定的 Azure 存储区域。 但是，这允许流量到所选 Azure 存储区域中的任何帐户。
  
  终结点策略允许您指定允许虚拟网络出站访问的 Azure 存储帐户，并限制对所有其他存储帐户的访问。 这为保护数据从虚拟网络中渗漏提供了更加精细的安全控制。

- __可缩放、高可用性的策略可以筛选 Azure 服务流量__

   终结点策略提供可横向缩放、高可用性的解决方案来筛选虚拟网络通过服务终结点发出的 Azure 服务流量。 为虚拟网络中的此流量维护中心网络设备不会产生额外的开销。

## <a name="json-object-for-service-endpoint-policies"></a>用于服务终结点策略的 JSON 对象
让我们快速查看服务终结点策略对象。

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Configuration

-   可以配置终结点策略，将虚拟网络流量限制为特定的 Azure 存储帐户。
-   终结点策略在虚拟网络中的某个子网上配置。 应在子网上启用 Azure 存储的服务终结点以应用策略。
-   终结点策略允许您使用 resourceID 格式添加特定的 Azure 存储帐户以允许列表。 您可以限制对
    - 订阅中的所有存储帐户<br>
      `E.g. /subscriptions/subscriptionId`

    - 资源组中的所有存储帐户<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - 通过列出相应的 Azure 资源管理器资源 Id 来单个存储帐户。 这包括发往 Blob、表、队列、文件和 Azure Data Lake Storage Gen2 的流量。 <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   默认情况下，如果没有策略附加到具有终结点的子网，则可以访问服务中的所有存储帐户。 在该子网上配置策略后，只能从该子网中的计算实例访问策略中指定的资源。 将被拒绝访问所有其他存储帐户。
-   在子网上应用服务终结点策略时，Azure 存储*服务终结点作用域*将从区域升级到**全局**。 这意味着 Azure 存储的所有流量都通过服务终结点进行保护。 服务终结点策略也全局适用，因此任何不允许显式访问的存储帐户都将被拒绝访问。 
-   可将多个策略应用到一个子网。 当多个策略与子网关联时，将允许虚拟网络流量到任何这些策略中指定的资源。 将拒绝访问未在任何策略中指定的其他所有服务资源。

    > [!NOTE]  
    > 服务终结点策略是**允许策略**，因此除了指定的资源外，所有其他资源都受到限制。 请确保在策略中标识并列出应用程序的所有服务资源依赖项。

- 只能在终结点策略中指定使用 Azure 资源模型的存储帐户。 您经典的 Azure 存储帐户将不支持 Azure 服务终结点策略。
- 如果列出了主要帐户，则自动允许 RA-GRS 辅助访问。
- 存储帐户可以位于虚拟网络所在的相同或不同订阅或 Azure Active Directory 租户中。

## <a name="scenarios"></a>方案

- **对等互连、互连或多个虚拟网络**：若要筛选对等虚拟网络中的流量，应该单独对这些虚拟网络应用终结点策略。
- **使用网络设备或 Azure 防火墙筛选 Internet 流量**：使用策略筛选 Azure 服务流量，通过服务终结点筛选 Azure 服务流量，并通过设备或 Azure 防火墙筛选 Internet 或 Azure 流量的其余部分。
- **筛选部署到虚拟网络中的 Azure 服务的流量**：此时，部署到虚拟网络的任何托管 Azure 服务都不支持 Azure 服务终结点策略。 
- **筛选从本地发往 Azure 服务的流量**：服务终结点策略只会应用到与这些策略关联的子网发出的流量。 若要允许从本地访问特定的 Azure 服务资源，应使用网络虚拟设备或防火墙筛选流量。

## <a name="logging-and-troubleshooting"></a>日志记录和故障排除
任何集中式日志记录均适用于服务终结点策略。 有关服务诊断日志，请参阅[服务终结点日志记录](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting)。

### <a name="troubleshooting-scenarios"></a>故障排除方案
- 拒绝访问在预览版中工作的存储帐户（不在地理配对区域中）
  - 通过 Azure 存储升级以使用全局服务标记，服务终结点和服务终结点策略的范围现在为全局。 因此，对 Azure 存储的任何流量都通过服务终结点加密，只允许访问策略中显式列出的存储帐户。
  - 显式允许列出还原访问权限所需的所有存储帐户。  
  - 联系 Azure 支持部门。
- 拒绝终结点策略中列出的帐户进行访问
  - 网络安全组或防火墙筛选可能会阻止访问
  - 如果删除/重新应用策略导致连接断开：
    - 验证 Azure 服务是否配置为允许从虚拟网络通过终结点进行访问，或者资源的默认策略是否设置为 *"允许全部*"。
    - 验证服务诊断是否显示通过终结点传送的流量。
    - 检查网络安全组流日志是否显示访问活动，以及存储日志是否按预期显示通过服务终结点进行的访问。
    - 联系 Azure 支持部门。
- 拒绝服务终结点策略中未列出的帐户进行访问
  - 验证 Azure 存储是否配置为允许从虚拟网络通过终结点进行访问，或者资源的默认策略是否设置为 *"允许全部*"。
  - 确保这些帐户不是具有子网上服务终结点策略**的经典存储帐户**。
- 在子网上应用服务终结点策略后，托管 Azure 服务停止工作
  - 此时服务终结点策略不支持托管服务。 *请观看此空间以获取更新*。

## <a name="provisioning"></a>设置

对虚拟网络拥有写入访问权限的用户可在子网上配置服务终结点策略。 详细了解 Azure [内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)以及如何将特定的权限分配到[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

虚拟网络和 Azure 存储帐户可以位于相同或不同的订阅中，也可以位于 Azure 活动目录租户中。

## <a name="limitations"></a>限制

- 只能在通过 Azure 资源管理器部署模型部署的虚拟网络中部署服务终结点策略。
- 虚拟网络必须与服务终结点策略位于同一区域。
- 如果为策略中列出的 Azure 服务配置了服务终结点，则只能在子网上应用服务终结点策略。
- 不能对从本地网络发往 Azure 服务的流量使用服务终结点策略。
- Azure 托管服务当前不支持终结点策略。 这包括部署到共享子网的托管服务（例如*Azure HDInsight、Azure 批处理、Azure ADDS、Azure APplication 网关、Azure VPN 网关、Azure 防火墙*）或专用子网（例如*Azure 应用服务环境、Azure Redis 缓存、Azure API 管理、Azure SQL MI、经典托管服务*）。

 > [!WARNING]
 > 部署到虚拟网络中的 Azure 服务（例如 Azure HDInsight）出于基础结构要求的访问其他 Azure 服务（例如 Azure 存储）。 将终结点策略限制为特定的资源可能会中断虚拟网络中部署的 Azure 服务对这些基础结构资源的访问。

- 终结点策略不支持经典存储帐户。 默认情况下，策略将拒绝对所有经典存储帐户的访问。 如果应用程序需要访问 Azure 资源管理器和经典存储帐户，则不应该对此流量使用终结点策略。

## <a name="pricing-and-limits"></a>定价和限制

使用服务终结点策略不会产生额外的费用。 Azure 服务（例如 Azure 存储）的当前定价模型保持不变，高于服务终结点。

对服务终结点策略强制实施以下限制： 

 |资源 | 默认限制 |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>后续步骤

- 了解如何[配置虚拟网络服务终结点策略](virtual-network-service-endpoint-policies-portal.md)
- 了解有关[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)的更多信息
