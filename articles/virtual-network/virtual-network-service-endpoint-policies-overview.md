---
title: Azure 虚拟网络服务终结点策略 | Microsoft Docs
description: 了解如何使用服务终结点策略来筛选发往 Azure 服务资源的虚拟网络流量
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumeet.mittal
ms.openlocfilehash: 7a3a94e9759dfb3c525ffcf1e840d5bec18f4808
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051305"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>虚拟网络服务终结点策略（预览版）

使用虚拟网络 (VNet) 服务终结点策略可以通过服务终结点筛选发往 Azure 服务的虚拟网络流量，以便仅允许特定的 Azure 服务资源。 终结点策略针对发往 Azure 服务的虚拟网络流量提供精细的访问控制。

此功能以__预览版__的形式针对以下 Azure 服务和区域提供：

__Azure 存储__：WestCentralUS、WestUS2。

有关预览版的最新通知，请参阅 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)页。

> [!NOTE]  
> 在预览期，虚拟网络服务终结点策略的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="key-benefits"></a>主要优点

虚拟网络服务终结点策略提供以下优势：

- __改进了发往 Azure 服务的虚拟网络流量的安全性__

  使用[网络安全组的 Azure 服务标记](https://aka.ms/servicetags)可以限制发往特定 Azure 服务的虚拟网络出站流量。 但是，这会允许流量发送到该 Azure 服务的所有资源。 
  
  现在，可以使用终结点策略限制为只允许对特定的 Azure 资源进行虚拟网络出站访问。 这可以提供精细得多的安全控制，为虚拟网络中访问的数据提供保护。 

- __可缩放、高可用性的策略可以筛选 Azure 服务流量__

   终结点策略提供可横向缩放、高可用性的解决方案来筛选虚拟网络通过服务终结点发出的 Azure 服务流量。 为虚拟网络中的此流量维护中心网络设备不会产生额外的开销。

## <a name="configuration"></a>配置

- 可将终结点策略配置为只允许将虚拟网络流量发送到特定的 Azure 服务资源。 预览版支持对 Azure 存储使用终结点策略。 
- 终结点策略在虚拟网络中的某个子网上配置。 应该在该子网上启用服务终结点，才能对策略中列出的所有 Azure 服务应用该策略。
- 终结点策略允许使用 resourceID 格式将特定的 Azure 服务资源加入允许列表。 可以限制对订阅或资源组中所有资源的访问。 还可以限制对特定资源的访问。 
- 默认情况下，如果未将任何策略附加到包含终结点的子网，则可以访问服务中的所有资源。 在该子网上配置策略后，只能从该子网中的计算实例访问策略中指定的资源。 将拒绝访问特定服务的其他所有资源。 
- 可以筛选发往配置了服务终结点的区域中的 Azure 服务资源的流量。 默认情况下，允许访问其他区域中的服务资源。 

  > [!NOTE]  
  > 若要完全锁定对服务终结点区域中 Azure 资源的虚拟网络出站访问，还需要使用[服务标记](security-overview.md#service-tags)配置网络安全组规则，以便仅允许将流量发送到服务终结点区域。

- 可将多个策略应用到一个子网。 将多个策略关联到该子网后，对于同一个服务，将允许虚拟网络流量发送到其中任一策略中指定的资源。 将拒绝访问未在任何策略中指定的其他所有服务资源。 

  > [!NOTE]  
  > 策略仅允许从虚拟网络访问列出的服务资源。 将特定的资源添加到策略时，将自动拒绝发往服务的其他所有流量。 请确保可以识别并在策略中列出应用程序的所有服务资源依赖关系。

  > [!WARNING]  
  > 部署到虚拟网络中的 Azure 服务（例如 Azure HDInsight）出于基础结构要求的访问其他 Azure 服务（例如 Azure 存储）。 将终结点策略限制为特定的资源可能会中断虚拟网络中部署的服务对这些基础结构资源的访问。 有关特定的服务，请参阅[限制](#limitations)。在预览期，虚拟网络中部署的任何 Azure 托管服务不支持服务终结点策略。

- 对于 Azure 存储： 
  -  可以通过列出存储帐户的 Azure 资源管理器 *resourceId* 来限制访问。 这包括发往 Blob、表、队列、文件和 Azure Data Lake Storage Gen2 的流量。

     例如，可按如下所示在终结点策略定义中列出 Azure 存储帐户：
    
     若要允许特定的存储帐户：         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     若要允许订阅和资源组中的所有帐户：`/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     若要允许订阅中的所有帐户：`/subscriptions/subscriptionId`
    
- 只能在终结点策略中指定使用 Azure 资源模型的存储帐户。  

  > [!NOTE]  
  > 使用终结点策略阻止对经典存储帐户的访问。

- 所列帐户的主要位置应在子网的服务终结点的地理配对区域中。 

  > [!NOTE]  
  > 策略允许指定其他区域中的服务资源。 只能根据地理配对区域筛选对 Azure 服务的虚拟网络访问。 如果网络安全组未限制为 Azure 存储的地理配对区域，则虚拟网络可以访问地理配对区域外部的所有存储帐户。

- 如果列出了主要帐户，则自动允许 RA-GRS 辅助访问。 
- 存储帐户可以位于虚拟网络所在的相同或不同订阅或 Azure Active Directory 租户中。 

## <a name="limitations"></a>限制

- 只能在通过 Azure 资源管理器部署模型部署的虚拟网络中部署服务终结点策略。
- 虚拟网络必须与服务终结点策略位于同一区域。
- 如果为策略中列出的 Azure 服务配置了服务终结点，则只能在子网上应用服务终结点策略。
- 不能对从本地网络发往 Azure 服务的流量使用服务终结点策略。
- 不应该出于基础结构的要求，对包含依赖于 Azure 服务的 Azure 托管服务的子网应用终结点策略。 

  > [!WARNING]  
  > 部署到虚拟网络中的 Azure 服务（例如 Azure HDInsight）出于基础结构要求的访问其他 Azure 服务（例如 Azure 存储）。 将终结点策略限制为特定的资源可能会中断虚拟网络中部署的 Azure 服务对这些基础结构资源的访问。
  
  - 某些 Azure 服务可以部署到包含其他计算实例的子网。 如果下面列出的托管服务已部署到子网，请确保不要对该子网应用终结点策略。
   
    - Azure HDInsight
    - Azure Batch（Azure 资源管理器）
    - Azure Active Directory 域服务（Azure 资源管理器）
    - Azure 应用程序网关（Azure 资源管理器）
    - Azure VPN 网关（Azure 资源管理器）
    - Azure 防火墙

  - 某些 Azure 服务会部署到专用子网。 在预览期，下面列出的所有此类服务将阻止终结点策略。 

     - Azure 应用服务环境
     - Azure Rediscache
     - Azure API 管理
     - Azure SQL 托管实例
     - Azure Active Directory 域服务
     - Azure 应用程序网关（经典）
     - Azure VPN 网关（经典）

- Azure 存储：终结点策略不支持经典存储帐户。 默认情况下，策略将拒绝对所有经典存储帐户的访问。 如果应用程序需要访问 Azure 资源管理器和经典存储帐户，则不应该对此流量使用终结点策略。 

## <a name="nsgs-with-service-endpoint-policies"></a>包含服务终结点策略的 NSG
- 默认情况下，NSG 允许出站 Internet 流量，包括发往 Azure 服务的虚拟网络流量。
- 若要拒绝所有出站 Internet 流量并只允许发往特定 Azure 服务的流量： 

  步骤 1：使用 *Azure 服务标记*将 NSG 配置为仅允许发往终结点区域中 Azure 服务的出站流量。 有关详细信息，请参阅 [NSG 的服务标记](https://aka.ms/servicetags)
      
  限制为仅允许访问终结点区域的网络安全组规则如以下示例所示：

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  步骤 2：应用仅允许访问特定 Azure 服务资源的服务终结点策略。

  > [!WARNING]  
  > 如果网络安全组未配置为限制 Azure 服务通过虚拟网络对对终结点区域的访问，则即使应用了服务终结点策略，你也可以访问其他区域中的服务资源。

## <a name="scenarios"></a>方案

- **对等互连、互连或多个虚拟网络**：若要筛选对等互连虚拟网络中的流量，应该单独对这些虚拟网络应用终结点策略。
- **使用网络设备或 Azure 防火墙筛选 Internet 流量**：通过终结点使用策略筛选 Azure 服务流量，并通过设备或 Azure 防火墙筛选剩余的 Internet 或 Azure 流量。 
- **基于部署到虚拟网络中的 Azure 服务筛选流量**：在预览期间，虚拟网络中部署的任何 Azure 托管服务不支持服务终结点策略。 
 有关特定的服务，请参阅[限制](#Limitations)。
- **筛选从本地发往 Azure 服务的流量**：服务终结点策略只会应用到与这些策略关联的子网发出的流量。 若要允许从本地访问特定的 Azure 服务资源，应使用网络虚拟设备或防火墙筛选流量。

## <a name="logging-and-troubleshooting"></a>日志记录和故障排除
任何集中式日志记录均适用于服务终结点策略。 有关服务诊断日志，请参阅[服务终结点日志记录](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting)。

### <a name="troubleshooting-scenarios"></a>故障排除方案
- 允许终结点策略中未列出的存储帐户进行访问
  - 网络安全组可能允许访问 Internet 或其他区域中的 Azure 存储帐户。
  - 网络安全组应配置为拒绝所有出站 Internet 流量，并只允许发往特定 Azure 存储区域的流量。 有关详细信息，请参阅[网络安全组](#network-security-groups)。
- 拒绝终结点策略中列出的帐户进行访问
  - 网络安全组或防火墙筛选可能会阻止访问
  - 如果删除/重新应用策略导致连接断开：
   - 验证 Azure 服务是否配置为允许虚拟网络通过终结点进行访问，或者资源的默认策略是否设置为“全部允许”。
      > [!NOTE]      
      > 无需在虚拟网络中保护服务资源即可通过终结点策略进行访问。 但是，作为安全最佳做法，我们建议在受信任的网络（例如 Azure 虚拟网络）中通过服务终结点以及在本地通过 IP 防火墙来保护服务资源。
  
   - 验证服务诊断是否显示通过终结点传送的流量。
    - 检查网络安全组流日志是否显示访问活动，以及存储日志是否按预期显示通过服务终结点进行的访问。
    - 联系 Azure 支持部门。
- 拒绝服务终结点策略中未列出的帐户进行访问
  - 网络安全组或防火墙筛选可能会阻止访问。 确保终结点区域允许“Azure 存储”服务标记。 有关策略限制，请参阅[限制](#limitations)。
  例如，如果应用了策略，则会拒绝经典存储帐户的访问。
  - 验证 Azure 服务是否配置为允许虚拟网络通过终结点进行访问，或者资源的默认策略是否设置为“全部允许”。

## <a name="provisioning"></a>设置

对虚拟网络拥有写入访问权限的用户可在子网上配置服务终结点策略。 详细了解 Azure [内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)以及如何将特定的权限分配到[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

虚拟网络和 Azure 服务资源可以位于相同或不同的订阅或 Azure Active Directory 租户中。 

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
- 详细了解[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)

