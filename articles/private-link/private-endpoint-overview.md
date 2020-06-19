---
title: 什么是 Azure 专用终结点？
description: 了解 Azure 专用终结点
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 51afa118be75c7e9ea2cb6e394d27cc39a58de0b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849648"
---
# <a name="what-is-azure-private-endpoint"></a>什么是 Azure 专用终结点？

Azure 专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 该服务可以是 Azure 服务，例如 Azure 存储、Azure Cosmos DB、SQL 等，也可以是你自己的[专用链接服务](private-link-service-overview.md)。
  
## <a name="private-endpoint-properties"></a>专用终结点属性 
 专用终结点指定以下属性： 


|properties  |说明 |
|---------|---------|
|名称    |    资源组内的唯一名称。      |
|子网    |  要从虚拟网络中部署和分配专用 IP 地址的子网。 有关子网要求，请参阅本文中的“限制”部分。         |
|专用链接资源    |   要使用资源 ID 或别名连接的专用链接资源，其类型为可用类型列表中的类型。 将为发送到此资源的所有流量生成一个唯一的网络标识符。       |
|目标子资源   |      要连接的子资源。 每个专用链接资源类型都提供多个不同选项，可根据偏好进行选择。    |
|连接审批方法    |  自动或手动。 根据基于角色的访问控制 (RBAC) 权限，可以自动批准专用终结点。 如果尝试在没有 RBAC 的情况下连接到专用链接资源，请使用手动方法来允许资源所有者批准连接。        |
|请求消息     |  可以指定手动审批有关连接请求的消息。 此消息可用于标识特定请求。        |
|连接状态   |   一个只读属性，指定专用终结点是否处于活动状态。 只有处于已批准状态的专用终结点才能用于发送流量。 其他可用的状态： <br>-**已批准**：连接已自动或手动批准，可供使用。</br><br>-**待批准**：连接是手动创建的，正在等待专用链接资源所有者批准。</br><br>-**已拒绝**：连接被专用链接资源所有者拒绝。</br><br>-**已断开连接**：连接被专用链接资源所有者删除。 专用终结点已变为信息性的，应出于清理目的将其删除。 </br>|

下面是有关专用终结点的一些重要详细信息： 
- 专用终结点使同一 VNet、区域性对等互连的 Vnet、全局性对等互连的 Vnet 中的以及本地用户能够使用 [VPN](https://azure.microsoft.com/services/vpn-gateway/) 或 [Express 路由](https://azure.microsoft.com/services/expressroute/)和由专用链接提供支持的服务在用户之间实现互连。
 
- 只有连接到专用终结点的客户端能发起网络连接，服务提供程序不提供任何路由配置用于发起服务使用者之间的互连。 只能在单个方向建立连接。

- 创建专用终结点时，还会创建一个在资源的生命周期内有效的只读网络接口。 将从映射到专用链接资源的子网中向该接口动态分配专用 IP 地址。 专用 IP 地址的值在专用终结点的整个生命周期中保持不变。
 
- 专用终结点必须与虚拟网络部署在同一区域中。 
 
- 专用链接资源可部署在与虚拟网络和专用终结点不同的区域中。
 
- 可以使用相同的专用链接资源创建多个专用终结点。 对于使用常见 DNS 服务器配置的单个网络，建议的做法是为给定的专用链接资源使用单个专用终结点，以避免在 DNS 解析中出现重复条目或发生冲突。 
 
- 可以在同一虚拟网络中的相同或不同子网中创建多个专用终结点。 可以在订阅中创建的专用终结点的数量存在限制。 有关详细信息，请参阅  [Azure 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


 
## <a name="private-link-resource"></a>专用链接资源 
专用链接资源是给定的专用终结点的目标。 下面是可用的私有链接资源类型的列表： 
 
|专用链接资源名称  |资源类型   |子资源  |
|---------|---------|---------|
|**专用链接服务**（你自己的服务）   |  Microsoft.Network/privateLinkServices       | empty |
|**Azure SQL 数据库** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Azure 存储**  | Microsoft.Storage/storageAccounts    |  Blob（blob、blob_secondary）<BR> 表（表、table_secondary）<BR> 队列（队列、queue_secondary）<BR> 文件（文件、file_secondary）<BR> Web（web、web_secondary）        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob（blob、blob_secondary）<BR> Data Lake File System Gen2（dfs、dfs_secondary）       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql、MongoDB、Cassandra、Gremlin、表|
|**Azure Database for PostgreSQL - 单一服务器** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure IoT 中心** | Microsoft.Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | 保管库 |
|**Azure Kubernetes 服务 - Kubernetes API** | Microsoft.ContainerService/managedClusters    | managedCluster |
|**Azure 搜索** | Microsoft.Search/searchService| searchService|  
|**Azure 容器注册表** | Microsoft.ContainerRegistry/registries    | 注册表 |
|**Azure 应用配置** | Microsoft.Appconfiguration/configurationStores    | configurationStore |
|**Azure 备份** | Microsoft.RecoveryServices/vaults    | 保管库 |
|**Azure 事件中心** | Microsoft.EventHub/namespaces    | 命名空间 |
|**Azure 服务总线** | Microsoft.ServiceBus/namespaces | 命名空间 |
|**Azure 中继** | Microsoft.Relay/namespaces | 命名空间 |
|**Azure 事件网格** | Microsoft.EventGrid/topics    | 主题 |
|**Azure 事件网格** | Microsoft.EventGrid/domains    | 域 |
|**Azure Web 应用** | Microsoft.Web/sites    | site |
|**Azure 机器学习** | Microsoft.MachineLearningServices/workspaces    | 工作区 |
  
 
## <a name="network-security-of-private-endpoints"></a>专用终结点的网络安全性 
为 Azure 服务使用专用终结点时，流向特定专用链接资源的流量会受到保护。 平台会执行访问控制来验证网络连接仅到达指定的专用链接资源。 若要访问同一 Azure 服务中的其他资源，需要额外的专用终结点。 
 
可以完全锁定工作负载，使其无法访问试图连接到某项受支持的 Azure 服务的公共终结点。 此控制提供内置的渗透防护，可阻止对同一 Azure 服务上托管的其他资源进行访问，从而为资源提供了额外的网络安全层。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>使用审批工作流访问专用链接资源 
可以使用以下连接审批方法连接到专用链接资源：
- **自动**：如果拥有或具有对特定专用链接资源的权限，则自动批准。 所需的权限基于专用链接资源类型，格式如下：Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionApproval/action
- **手动**：如果不具有所需的权限并想请求访问权限，则手动请求。 这时将启动审批工作流。 将会以“挂起”状态创建专用终结点和后续的专用终结点连接。 专用链接资源所有者负责审批该连接。 获得批准后，专用终结点即可正常发送流量，如以下审批工作流图所示。  

![工作流审批](media/private-endpoint-overview/private-link-paas-workflow.png)
 
专用链接资源所有者可以对专用终结点连接执行以下操作： 
- 查看所有专用终结点连接的详细信息。 
- 批准专用终结点连接。 相应的专用终结点便能够向专用链接资源发送流量。 
- 拒绝专用终结点连接。 将更新相应的专用终结点以反映最新状态。
- 删除任何状态的专用终结点连接。 相应的专用终结点将更新为“断开连接”状态以反映该操作的结果，专用终结点所有者此时只能删除资源。 
 
> [!NOTE]
> 只有处于已批准状态的专用终结点才能将流量发送到给定的专用链接资源。 

### <a name="connecting-using-alias"></a>使用别名进行连接
别名是一个唯一的名字对象，当服务所有者在标准负载均衡器后创建专用链接服务时，将生成该名字对象。 服务所有者可在脱机状态下与其使用者共享此别名。 使用者可以使用资源 URI 或别名请求连接到专用链接服务。 如果要使用别名进行连接，需要使用手动连接批准方法创建专用终结点。 若要使用手动连接审批方法，请在专用终结点创建流期间将手动请求参数设置为 true。 有关详细信息，请查看 [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) 和 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)。 

## <a name="dns-configuration"></a>DNS 配置 
使用完全限定的域名 (FQDN) 作为连接字符串的一部分连接到专用链接资源时，正确配置 DNS 设置以解析为已分配的专用 IP 地址很重要。 现有 Azure 服务可能已具有通过公共终结点连接时要使用的 DNS 配置。 需要替代此配置才能使用专用终结点进行连接。 
 
与专用终结点关联的网络接口包含配置 DNS 所需的完整的一组信息，包括为给定专用链接资源分配的 FQDN 和专用 IP 地址。 

有关为专用终结点配置 DNS 的最佳实践和建议的完整详细信息，请查看[专用终结点 DNS 配置文章](private-endpoint-dns.md)。



 
## <a name="limitations"></a>限制
 
下表列出了使用专用终结点时的已知限制的列表： 


|限制 |说明 |缓解操作  |
|---------|---------|---------|
|网络安全组 (NSG) 规则和用户定义的路由不适用于专用终结点    |专用终结点不支持 NSG。 虽然包含专用终结点的子网可以具有与之关联的 NSG，但这些规则对专用终结点处理的流量不起作用。 必须禁用[网络策略强制](disable-private-endpoint-network-policy.md)功能，才能在子网中部署专用终结点。 同一子网上托管的其他工作负荷上仍强制实施 NSG。 任何客户端子网上的路由都将使用前缀“/32”，若要更改默认路由行为，需要类似的 UDR  | 通过在源客户端上对出站流量使用 NSG 规则来控制流量。 使用“/32”前缀来部署单个路由以替代专用终结点路由。 仍支持为出站连接提供 NSG 流日志和监视信息，并且可以使用这些日志和信息        |


## <a name="next-steps"></a>后续步骤
- [使用门户创建 SQL 数据库服务器的专用终结点](create-private-endpoint-portal.md)
- [使用 PowerShell 创建 SQL 数据库服务器的专用终结点](create-private-endpoint-powershell.md)
- [使用 CLI 创建 SQL 数据库服务器的专用终结点](create-private-endpoint-cli.md)
- [使用门户创建存储帐户的专用终结点](create-private-endpoint-storage-portal.md)
- [使用门户创建 Azure Cosmos 帐户的专用终结点](../cosmos-db/how-to-configure-private-endpoints.md)
- [使用 Azure PowerShell 创建自己的专用链接服务](create-private-link-service-powershell.md)
- [使用门户创建自己的用于 Azure Database for PostgreSQL - 单个服务器的专用链接](../postgresql/howto-configure-privatelink-portal.md)
- [使用 CLI 创建自己的用于 Azure Database for PostgreSQL - 单个服务器的专用链接](../postgresql/howto-configure-privatelink-cli.md)
- [使用门户创建自己的用于 Azure Database for MySQL 的专用链接](../mysql/howto-configure-privatelink-portal.md)
- [使用 CLI 创建自己的用于 Azure Database for MySQL 的专用链接](../mysql/howto-configure-privatelink-cli.md)
- [使用门户创建自己的用于 Azure Database for MariaDB 的专用链接](../mariadb/howto-configure-privatelink-portal.md)
- [使用 CLI 创建自己的用于 Azure Database for MariaDB 的专用链接](../mariadb/howto-configure-privatelink-cli.md)
