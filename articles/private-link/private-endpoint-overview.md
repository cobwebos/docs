---
title: 什么是 Azure 专用终结点？
description: 了解 Azure 专用终结点
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: fd389c1e909e6875ead8410b5ca692b82c79e0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063077"
---
# <a name="what-is-azure-private-endpoint"></a>什么是 Azure 专用终结点？

Azure 专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效地引入 VNet 中。 服务可以是 Azure 存储、Azure Cosmos DB、SQL 等 Azure 服务，也可以是你自己的[专用链接服务](private-link-service-overview.md)。
  
## <a name="private-endpoint-properties"></a>专用终结点属性 
 专用终结点指定以下属性： 


|properties  |描述 |
|---------|---------|
|“属性”    |    资源组中的唯一名称。      |
|子网    |  虚拟网络中要部署和分配专用 IP 地址的子网。 有关子网要求，请参阅本文中的“限制”部分。         |
|专用链接资源    |   用于通过可用类型列表中的资源 ID 或别名建立连接的专用链接资源。 将为发送到此资源的所有流量生成唯一的网络标识符。       |
|目标子资源   |      要连接的子资源。 每个专用链接资源类型具有不同的选项，可根据偏好做出选择。    |
|连接批准方法    |  自动或手动。 可以根据基于角色的访问控制 (RBAC) 权限自动批准专用终结点。 如果尝试在不使用 RBAC 的情况下连接到专用链接资源，请使用手动方法来允许资源所有者批准连接。        |
|请求消息     |  可为请求手动批准的连接指定消息。 此消息可用于标识特定的请求。        |
|连接状态   |   一个只读属性，指定专用终结点是否处于活动状态。 只能使用处于已批准状态的专用终结点发送流量。 可用的其他状态： <br>-**已批准**：连接已自动或手动批准，可供使用。</br><br>-**挂起**： 连接是手动创建的，正在等待专用链接资源所有者的批准。</br><br>-**已拒绝**：连接被专用链接资源所有者拒绝。</br><br>-**已断开连接**：连接已删除，由专用链接资源所有者删除。 专用终结点已变为参考性终结点，应将其删除以清理资源。 </br>|

下面是有关专用终结点的一些重要详细信息： 
- 专用终结点使用 [VPN](https://azure.microsoft.com/services/vpn-gateway/) 或 [Express Route](https://azure.microsoft.com/services/expressroute/) 以及专用链接驱动的服务，在同一 VNet、区域对等互连的 VNet、全球对等互连的 VNet 和本地的使用者之间实现连接。
 
- 创建专用终结点时，也会为资源的生命周期创建一个网络接口。 为该接口分配了子网中映射到专用链接服务的专用 IP 地址。
 
- 专用终结点必须部署在与虚拟网络相同的区域中。 
 
- 专用链接资源可部署在与虚拟网络和专用终结点不同的区域中。
 
- 可以使用同一个专用链接资源创建多个专用终结点。 对于使用常见 DNS 服务器配置的单个网络，建议的做法是对给定的专用链接资源使用单个专用终结点，以避免出现重复条目或 DNS 解析冲突。 
 
- 可以在同一虚拟网络中的相同或不同子网上创建多个专用终结点。 在一个订阅中可以创建的专用终结点数量有限制。 有关详细信息，请参阅  [Azure 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


 
## <a name="private-link-resource"></a>专用链接资源 
专用链接资源是给定专用终结点的目标。 下面是可用的专用链接资源类型列表： 
 
|专用链接资源名称  |资源类型   |子资源  |
|---------|---------|---------|
|**专用链接服务**（你自己的服务）   |  Microsoft.Network/privateLinkServices       | empty |
|**Azure SQL 数据库** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Azure 存储**  | Microsoft.Storage/storageAccounts    |  Blob（blob、blob_secondary）<BR> 表（table、table_secondary）<BR> 队列（queue、queue_secondary）<BR> 文件（file、file_secondary）<BR> Web（web、web_secondary）        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob（blob、blob_secondary）<BR> Data Lake File System Gen2（dfs、dfs_secondary）       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | SQL、MongoDB、Cassandra、Gremlin、表|
|**用于 PostgreSQL - 单一服务器的 Azure 数据库** | Microsoft.DBforPostgreSQL/servers   | 后格雷sqlServer |
|**MySQL 的 Azure 数据库** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | 马里亚德布拉塞 |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | 保管库 |
|**Azure 库伯奈斯服务 - 库伯内斯 API** | Microsoft.ContainerService/managedClusters | 托管群集 |
|**Azure 搜索** | 微软.搜索/搜索服务| 搜索服务|  
|**Azure 容器注册表** | Microsoft.ContainerRegistry/registries  | 注册表 |
|**Azure 应用配置** | 微软.应用程序配置/配置商店   | 配置存储 |
|**Azure 备份** | Microsoft.RecoveryServices/vaults   | 保管库 |
|**Azure 事件中心** | Microsoft.EventHub/namespaces    | namespace |
|**Azure 服务总线** | Microsoft.ServiceBus/namespaces | namespace |
|**Azure 中继** | Microsoft.Relay/namespaces | namespace |
|**Azure 事件网格** | Microsoft.EventGrid/topics  | 主题 |
|**Azure 事件网格** | Microsoft.EventGrid/domains | 域 |
|**Azure WebApps** | Microsoft.Web/sites    | sites |
 
## <a name="network-security-of-private-endpoints"></a>专用终结点的网络安全性 
使用 Azure 服务的专用终结点时，流量将受到特定专用链接资源的保护。 平台会执行访问控制，以验证网络连接是否仅抵达指定的专用链接资源。 若要访问同一 Azure 服务中的其他资源，需要附加的专用终结点。 
 
可以完全锁定工作负荷，使其无法访问公共终结点来连接受支持的 Azure 服务。 这种控制提供内置的渗透保护来防止访问同一 Azure 服务上托管的其他资源，因此可为资源提供额外的网络安全层。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>使用批准工作流访问专用链接资源 
可使用以下连接批准方法连接到专用链接资源：
- 当你拥有特定的专用链接资源或对其拥权限时**自动**批准。 所需的权限基于以下格式的专用链接资源类型：Microsoft。\<提供商>/<resource_type>/专用端接连接审批/操作
- 没有所需的权限并想要请求访问权限时**手动**发出请求。 将发起批准工作流。 将会以“挂起”状态创建专用终结点和后续的专用终结点连接。 专用链接资源所有者负责审批该连接。 获得批准后，将启用专用终结点来正常发送流量，如以下批准工作流图中所示。  

![工作流批准](media/private-endpoint-overview/private-link-paas-workflow.png)
 
专用链接资源所有者可以针对专用终结点连接执行以下操作： 
- 评审所有专用终结点连接详细信息。 
- 批准专用终结点连接。 将启用相应的专用终结点，以将流量发送到专用链接资源。 
- 拒绝专用终结点连接。 相应的专用终结点将会更新以反映状态。
- 删除处于任何状态的专用终结点连接。 将使用已断开连接状态更新相应的专用终结点以反映操作，专用终结点所有者此时只能删除资源。 
 
> [!NOTE]
> 只有处于已批准状态的专用终结点才能将流量发送到给定的专用链接资源。 

### <a name="connecting-using-alias"></a>使用别名进行连接
别名是当服务所有者在标准负载均衡器后面创建专用链接服务时，生成的唯一名字对象。 服务所有者可与其使用者脱机共享此别名。 使用者可以使用资源 URI 或别名请求连接到专用链接服务。 若要使用别名进行连接，必须使用手动连接批准方法创建专用终结点。 若要使用手动连接批准方法，请在专用终结点创建流期间将手动请求参数设置为 true。 有关详细信息，请查看 [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) 和 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)。 

## <a name="dns-configuration"></a>DNS 配置 
使用完全限定的域名 (FQDN) 作为连接字符串的一部分连接到专用链接资源时，必须正确配置 DNS 设置，以解析为分配的专用 IP 地址。 现有的 Azure 服务可能已有在通过公共终结点进行连接时要使用的 DNS 配置。 需要替代此配置，以使用专用终结点进行连接。 
 
与专用终结点关联的网络接口包含配置 DNS 所需的完整信息，其中包括为给定专用链接资源分配的 FQDN 和专用 IP 地址。 
 
可使用以下选项来配置专用终结点的 DNS 设置： 
- **使用主机文件（仅建议用于测试）**。 可以使用虚拟机上的主机文件来替代 DNS。  
- **使用专用 DNS 区域**。 可以使用专用 DNS 区域来替代给定专用终结点的 DNS 解析。 可将专用 DNS 区域链接到虚拟网络，以解析特定的域。
- **使用自定义 DNS 服务器**。 可以使用自己的 DNS 服务器来替代给定专用链接资源的 DNS 解析。 如果 [DNS 服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)托管在虚拟网络上，可以创建 DNS 转发规则，以使用专用 DNS 区域来简化所有专用链接资源的配置。
 
> [!IMPORTANT]
> 不建议替代正在用于解析公共终结点的区域。 在不 DNS 转发到公共 DNS 的情况下，与资源的连接无法正确解析。 若要避免出现问题，请创建不同的域名，或对以下每个服务采用建议的名称。 
 
对于 Azure 服务，请根据下表中所述使用建议的区域名称：

|专用链接资源类型   |子资源  |区域名称  |
|---------|---------|---------|
|SQL DB（微软.Sql/服务器）    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure 突触分析（微软.Sql/服务器）    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|存储帐户 (Microsoft.Storage/storageAccounts)    |  Blob（blob、blob_secondary）        |    privatelink.blob.core.windows.net      |
|存储帐户 (Microsoft.Storage/storageAccounts)    |    表（table、table_secondary）      |   privatelink.table.core.windows.net       |
|存储帐户 (Microsoft.Storage/storageAccounts)    |    队列（queue、queue_secondary）     |   privatelink.queue.core.windows.net       |
|存储帐户 (Microsoft.Storage/storageAccounts)   |    文件（file、file_secondary）      |    privatelink.file.core.windows.net      |
|存储帐户 (Microsoft.Storage/storageAccounts)     |  Web（web、web_secondary）        |    privatelink.web.core.windows.net      |
|Data Lake File System Gen2 (Microsoft.Storage/storageAccounts)  |  Data Lake File System Gen2（dfs、dfs_secondary）        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|表|privatelink.table.cosmos.azure.com|
|后数据库的 Azure 数据库 - 单个服务器（微软.DBforpostsql/服务器）|后格雷sqlServer|privatelink.postgres.database.azure.com|
|MySQL 的 Azure 数据库（微软.DBforMySQL/服务器）|mysqlServer|privatelink.mysql.database.azure.com|
|MariaDB 的 Azure 数据库（微软.DBforMariaDB/服务器）|马里亚德布拉塞|privatelink.mariadb.database.azure.com|
|Azure 密钥保管库（微软.密钥库/保管库）|保管库|privatelink.vaultcore.azure.net|
|Azure 库伯奈斯服务 - 库伯奈斯 API（微软.容器服务/托管群集） | 托管群集 | [guid].私人链接。<region>.azmk8s.io|
|Azure 搜索（微软.搜索/搜索服务）|搜索服务|privatelink.search.windows.net|   
|Azure 容器注册表（微软.集装箱注册/注册表） | 注册表 | privatelink.azurecr.io |
|Azure 应用配置（微软.应用配置/配置存储）| 配置存储 | privatelink.azconfig.io|
|Azure 备份（微软.恢复服务/保管库）| 保管库 |私人链接。[地区].备份.windowsazure.com|
|Azure 事件中心（微软.事件中心/命名空间）| namespace |privatelink.servicebus.windows.net|
|Azure 服务总线（微软.服务总线/命名空间） | namespace |privatelink.servicebus.windows.net|
|Azure 中继（微软.中继/命名空间） | namespace |privatelink.servicebus.windows.net|
|Azure 事件网格（微软.事件网格/主题）   | 主题 | 主题。[区域].私人链接.事件网格.azure.net|
|Azure 事件网格（微软.事件网格/域） | 域 | 域。[区域].私人链接.事件网格.azure.net |
|Azure WebApps（微软.网站/网站） | site | privatelink.azurewebsites.net |
 
Azure 将在公共 DNS 中创建规范名称 DNS 记录 (CNAME)，以将解析重定向到建议的域名。 可以使用专用终结点的专用 IP 地址替代解析。 
 
应用程序无需更改连接 URL。 尝试使用公共 DNS 进行解析时，DNS 服务器现将解析为专用终结点。 此过程不影响应用程序。 
 
## <a name="limitations"></a>限制
 
下表列出了使用专用终结点时的已知限制： 


|限制 |描述 |缓解操作  |
|---------|---------|---------|
|网络安全组 (NSG) 规则和用户定义的路由不适用于专用终结点    |专用终结点不支持 NSG。 尽管包含专用终结点的子网可以有关联的 NSG，但这些规则不会针对专用终结点处理的流量生效。 必须[禁用网络策略的强制实施](disable-private-endpoint-network-policy.md)，才能在子网中部署专用终结点。 NSG 仍会在同一子网中托管的其他工作负荷上强制实施。 任何客户端子网上的路由将使用 /32 前缀，更改默认路由行为需要类似的 UDR  | 对源客户端上的出站流量使用 NSG 规则来控制流量。 使用 /32 前缀部署单个路由以覆盖专用终结点路由。 NSG 流日志和出站连接监视信息仍然受支持，可以使用        |


## <a name="next-steps"></a>后续步骤
- [使用门户创建 SQL 数据库服务器的专用终结点](create-private-endpoint-portal.md)
- [使用 PowerShell 创建 SQL 数据库服务器的专用终结点](create-private-endpoint-powershell.md)
- [使用 CLI 创建 SQL 数据库服务器的专用终结点](create-private-endpoint-cli.md)
- [使用门户创建存储帐户的专用终结点](create-private-endpoint-storage-portal.md)
- [使用门户创建 Azure Cosmos 帐户的专用终结点](../cosmos-db/how-to-configure-private-endpoints.md)
- [使用 Azure PowerShell 创建自己的专用链接服务](create-private-link-service-powershell.md)
- [为 PostgreSQL 的 Azure 数据库创建您自己的专用链接 - 使用门户创建单个服务器](../postgresql/howto-configure-privatelink-portal.md)
- [为后格雷SQL 的 Azure 数据库创建您自己的专用链接 - 使用 CLI 创建单个服务器](../postgresql/howto-configure-privatelink-cli.md)
- [使用门户为 MySQL 为 Azure 数据库创建您自己的专用链接](../mysql/howto-configure-privatelink-portal.md)
- [使用 CLI 为 MySQL 为 Azure 数据库创建您自己的专用链接](../mysql/howto-configure-privatelink-cli.md)
- [使用门户为 MariaDB 为 Azure 数据库创建自己的专用链接](../mariadb/howto-configure-privatelink-portal.md)
- [使用 CLI 为 MariaDB 为 Azure 数据库创建自己的专用链接](../mariadb/howto-configure-privatelink-cli.md)
