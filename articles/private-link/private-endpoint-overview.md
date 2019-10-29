---
title: 什么是 Azure 专用终结点？
description: 了解 Azure 专用终结点
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 7f0d846a83312e28c305100e7c8dc74cc8140d7d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023848"
---
# <a name="what-is-azure-private-endpoint"></a>什么是 Azure 专用终结点？

Azure 专用终结点是一个网络接口，该接口将你私下并安全地连接到由 Azure 专用链接提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址，从而有效地将服务引入 VNet。 服务可以是 azure 服务，例如 Azure 存储、SQL 等，也可以是你自己的[专用链接服务](private-link-service-overview.md)。
  
## <a name="private-endpoint-properties"></a>私有终结点属性 
 专用终结点指定以下属性： 


|properties  |描述 |
|---------|---------|
|名称    |    资源组中的唯一名称。      |
|子网    |  要从虚拟网络部署和分配专用 IP 地址的子网。 如需子网要求，请参阅本文中的限制部分。         |
|专用链接资源    |   从可用类型列表中使用资源 ID 或别名连接的专用链接资源。 将为发送到此资源的所有流量生成唯一的网络标识符。       |
|目标 subresource   |      要连接的 subresource。 每个专用链接资源类型都有不同的选项可根据首选项进行选择。    |
|连接批准方法    |  自动或手动。 根据基于角色的访问控制（RBAC）权限，可以自动批准专用终结点。 如果尝试在没有 RBAC 的情况下连接到专用链接资源，请使用手动方法来允许资源所有者批准连接。        |
|请求消息     |  你可以为请求的连接指定手动审批的消息。 此消息可用于标识特定请求。        |
|连接状态   |   一个只读属性，指定私有终结点是否处于活动状态。 只有处于已批准状态的私有终结点才能用于发送流量。 可用的附加状态： <br>-**批准**：已自动或手动批准连接，并且已准备好使用。</br><br>-**挂起**：连接是手动创建的，由专用链接资源所有者等待批准。</br><br>**拒绝**-：连接被私有链接资源所有者拒绝。</br><br>-**断开**连接：连接已被专用链接资源所有者删除。 专用终结点将变为信息性，应将其删除以进行清理。 </br>|

下面是有关专用终结点的一些重要详细信息： 
- 专用终结点允许使用者之间的连接与同一 VNet 中的突破对等互连 Vnet，全局对等互连 Vnet 和本地使用[VPN](https://azure.microsoft.com/services/vpn-gateway/)或[Express Route](https://azure.microsoft.com/services/expressroute/)提供的服务以及通过专用链接提供支持的服务。
 
- 创建专用终结点时，还会为资源的生命周期创建一个网络接口。 为接口分配了子网中的专用 IP 地址，该地址将映射到专用链接服务。
 
- 专用终结点必须与虚拟网络部署在同一区域中。 
 
- 专用链接资源可部署在与虚拟网络和专用终结点不同的区域中。
 
- 可以使用相同的专用链接资源创建多个专用终结点。 对于使用常见 DNS 服务器配置的单个网络，建议的做法是将单个专用终结点用于给定的专用链接资源，以避免在 DNS 解析中出现重复的条目或冲突。 
 
- 可以在同一虚拟网络中的相同或不同子网中创建多个专用终结点。 可以在订阅中创建的专用终结点的数量有限制。 有关详细信息，请参阅 [Azure 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)。


 
## <a name="private-link-resource"></a>专用链接资源 
专用链接资源是给定专用终结点的目标。 下面是可用的私有链接资源类型的列表： 
 
|专用链接资源名称  |资源类型   |子资源  |
|---------|---------|---------|
|**专用链接服务**（你自己的服务）   |  PrivateLinkServices/网络       | empty |
|**Azure SQL 数据库** | Microsoft.Sql/servers    |  Sql Server （sqlServer）        |
|**Azure SQL 数据仓库** | Microsoft.Sql/servers    |  Sql Server （sqlServer）        |
|**Azure 存储**  | Microsoft.Storage/storageAccounts    |  Blob （blob、blob_secondary）<BR> Table （table，table_secondary）<BR> Queue （queue，queue_secondary）<BR> 文件（file，file_secondary）<BR> Web （web、web_secondary）        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob （blob、blob_secondary）       |
 
 
## <a name="network-security-of-private-endpoints"></a>专用终结点的网络安全 
使用 Azure 服务的专用终结点时，流量将受到特定专用链接资源的保护。 平台会执行访问控制来验证仅到达指定的专用链接资源的网络连接。 若要访问同一 Azure 服务中的其他资源，需要额外的专用终结点。 
 
你可以完全锁定工作负载，使其无法访问公共终结点以连接到受支持的 Azure 服务。 此控制通过提供内置的渗透保护来向资源提供额外的网络安全层，阻止对同一 Azure 服务上托管的其他资源进行访问。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>使用审批工作流访问专用链接资源 
可以使用以下连接批准方法连接到专用链接资源：
- 当你拥有或具有特定专用链接资源的权限时**自动**批准。 所需的权限基于专用链接资源类型，格式如下： Microsoft。\<提供程序 >/< resource_type >/privateEndpointConnectionApproval/action
- 当你没有必需的权限，并且想要请求访问时，**手动**请求。 将启动审批工作流。 专用终结点和后续的专用终结点连接将在 "挂起" 状态下创建。 专用链接资源所有者负责批准连接。 获得批准后，会启用专用终结点以正常发送流量，如以下审批工作流关系图中所示。  

![工作流审批](media/private-endpoint-overview/private-link-paas-workflow.png)
 
专用链接资源所有者可以对专用终结点连接执行以下操作： 
- 查看所有专用终结点连接详细信息。 
- 批准专用终结点连接。 将启用相应的专用终结点，以将流量发送到专用链接资源。 
- 拒绝专用终结点连接。 相应的专用终结点将更新以反映状态。
- 删除处于任何状态的专用终结点连接。 将使用断开连接状态更新相应的专用终结点以反映该操作，专用终结点所有者此时只能删除该资源。 
 
> [!NOTE]
> 只有处于已批准状态的专用终结点才能将流量发送到给定的专用链接资源。 

### <a name="connecting-using-alias"></a>使用别名进行连接
别名是一个唯一的名字对象，当服务所有者在标准负载均衡器后创建专用链接服务时，将生成该名字对象。 服务所有者可与用户脱机共享此别名。 使用者可以使用资源 URI 或别名请求连接到专用链接服务。 如果要使用别名进行连接，则必须使用手动连接批准方法创建专用终结点。 对于使用手动连接批准方法，请在专用终结点创建流期间将手动请求参数设置为 true。 有关详细信息，请查看[AzPrivateEndpoint](https://docs.microsoft.com/en-us/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0)和[az 网络专用终结点创建](https://docs.microsoft.com/en-us/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)。 

## <a name="dns-configuration"></a>DNS 配置 
使用完全限定的域名（FQDN）连接到使用完全限定的域名（FQDN）作为连接字符串的一部分时，务必正确配置 DNS 设置以解析为分配的专用 IP 地址。 现有 Azure 服务可能已具有在通过公共终结点进行连接时要使用的 DNS 配置。 这需要重写以使用专用终结点进行连接。 
 
与专用终结点关联的网络接口包含配置 DNS 所需的完整信息集，其中包括为给定专用链接资源分配的 FQDN 和专用 IP 地址。 
 
你可以使用以下选项为专用终结点配置 DNS 设置： 
- **使用主机文件（仅建议用于测试）** 。 你可以使用虚拟机上的主机文件来替代 DNS。  
- **使用专用 DNS 区域**。 可以使用专用 DNS 区域替代给定专用终结点的 DNS 解析。 专用 DNS 区域可以链接到你的虚拟网络，以解析特定域。
- **使用自定义 DNS 服务器**。 你可以使用自己的 DNS 服务器替代给定专用链接资源的 DNS 解析。 如果你的[dns 服务器](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)托管在虚拟网络上，则可以创建 DNS 转发规则以使用专用 DNS 区域来简化所有专用链接资源的配置。
 
> [!IMPORTANT]
> 不建议重写主动用于解析公共终结点的区域。 如果没有 DNS 转发到公共 DNS，与资源的连接将无法正确解析。 若要避免出现问题，请创建一个不同的域名或遵循下面每个服务的建议名称。 
 
对于 Azure 服务，请使用下表中所述的建议区域名称：

|专用链接资源类型   |Subresource  |区域名称  |
|---------|---------|---------|
|SQL DB/DW （Sql/服务器）    |  Sql Server （sqlServer）        |   privatelink.database.windows.net       |
|存储帐户（storageAccounts/）    |  Blob （blob、blob_secondary）        |    privatelink.blob.core.windows.net      |
|存储帐户（storageAccounts/）    |    Table （table，table_secondary）      |   privatelink.table.core.windows.net       |
|存储帐户（storageAccounts/）    |    Queue （queue，queue_secondary）     |   privatelink.queue.core.windows.net       |
|存储帐户（storageAccounts/）   |    文件（file，file_secondary）      |    privatelink.file.core.windows.net      |
|存储帐户（storageAccounts/）     |  Web （web、web_secondary）        |    privatelink.web.core.windows.net      |
|Data Lake 文件系统 Gen2 （storageAccounts/）  |  Data Lake 文件系统 Gen2 （dfs、dfs_secondary）        |     privatelink.dfs.core.windows.net     |
||||
 

Azure 会在公共 DNS 上创建规范名称 DNS 记录（CNAME），以将解析重定向到建议的域名。 你将能够用专用终结点的专用 IP 地址替代解析。 
 
您的应用程序不需要更改连接 URL。 尝试使用公共 DNS 进行解析时，DNS 服务器现在会解析为你的专用终结点。 此过程不会影响你的应用程序。 
 
## <a name="limitations"></a>限制
 
下表列出了使用专用终结点时的已知限制： 


|限制 |描述 |缓解措施  |
|---------|---------|---------|
|网络安全组（NSG）规则和用户定义的路由不适用于专用终结点    |专用终结点不支持 NSG。 尽管包含专用终结点的子网可以有与之关联的 NSG，但这些规则对专用终结点处理的流量不起作用。 必须[禁用网络策略强制](disable-private-endpoint-network-policy.md)，才能在子网中部署专用终结点。 NSG 仍在同一子网上托管的其他工作负荷上强制实施。 任何客户端子网上的路由都将使用/32 前缀，更改默认路由行为时需要类似的 UDR  | 在源客户端上使用 NSG 规则控制流量。 部署具有/32 前缀的各个路由以替代专用终结点路由        |
|无法在针对服务终结点或专用工作负荷启用的子网中创建专用终结点    |无法在为服务终结点或委派到专用工作负荷的子网启用的子网上部署专用终结点|  创建一个单独的子网来部署专用终结点。        |
|专用终结点只能映射到同一区域中的专用链接服务（客户拥有）    |   不支持从其他区域连接到专用链接服务（你自己的）       |  预览期间，必须在同一区域中部署专用链接服务。        |
|  不支持仅具有专用终结点的对等互连虚拟网络   |   不支持在不使用任何其他工作负荷的情况下连接到对等互连虚拟网络上的专用终结点       | 在对等互连虚拟网络上部署单个 VM，以启用连接 |
|专用工作负荷无法访问专用终结点    |   以下部署到虚拟网络中的服务无法访问使用专用终结点的任何专用链接资源：<br>应用服务计划</br>Azure 容器实例</br>Azure NetApp 文件</br>Azure 专用 HSM<br>       |   预览期间没有缓解措施。       |


## <a name="next-steps"></a>后续步骤
- [使用门户创建 SQL 数据库服务器的专用终结点](create-private-endpoint-portal.md)
- [使用 PowerShell 创建 SQL 数据库服务器的专用终结点](create-private-endpoint-powershell.md)
- [使用 CLI 创建 SQL 数据库服务器的专用终结点](create-private-endpoint-cli.md)
- [使用门户创建存储帐户的专用终结点](create-private-endpoint-storage-portal.md)
- [使用 Azure PowerShell 创建自己的专用链接服务](create-private-link-service-powershell.md)
