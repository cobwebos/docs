---
title: Azure 专用终结点 DNS 配置
description: 了解 Azure 专用终结点 DNS 配置
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7c8ff0808ada522dc24ef3c27156f4151832fbcd
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715930"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure 专用终结点 DNS 配置


使用完全限定的域名 (FQDN) 作为连接字符串的一部分连接到专用链接资源时，正确配置 DNS 设置以解析为已分配的专用 IP 地址很重要。 现有 Azure 服务可能已具有通过公共终结点连接时使用的 DNS 配置。 需要替代此配置才能使用专用终结点进行连接。 
 
与专用终结点关联的网络接口包含配置 DNS 所需的完整的一组信息，包括为给定专用链接资源分配的 FQDN 和专用 IP 地址。 
 
可使用以下选项为专用终结点配置 DNS 设置： 
- **使用主机文件（仅推荐用于测试）** 。 可使用虚拟机上的主机文件来替代 DNS。  
- **使用专用 DNS 区域**。 可使用[专用 DNS 区域](../dns/private-dns-privatednszone.md)来替代给定专用终结点的 DNS 解析。 可将专用 DNS 区域链接到你的虚拟网络，以解析特定域。
- **使用 DNS 转发器（可选）** 。 可使用 DNS 转发器来替代给定专用链接资源的 DNS 解析。 如果 [DNS 服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)托管在虚拟网络上，则可以创建 DNS 转发规则，以使用专用 DNS 区域来简化所有专用链接资源的配置。
 
> [!IMPORTANT]
> 不推荐替代正用于解析公共终结点的区域。 如果 DNS 不转发到公共 DNS，将无法正确解析与资源的连接。 为了避免出现问题，请为下面每个服务创建不同的域名或使用建议的名称。 

## <a name="azure-services-dns-zone-configuration"></a>Azure 服务 DNS 区域配置
Azure 服务将在公共 DNS 上创建一个规范名称 DNS 记录 (CNAME)，以将解析重定向到建议的专用域名。 可以用专用终结点的专用 IP 地址替代解析。 
 
应用程序不需要更改连接 URL。 尝试使用公共 DNS 进行解析时，DNS 服务器现在会解析为你的专用终结点。 此过程不会影响现有应用程序。 

对于 Azure 服务，请使用下表中所述的建议区域名称：

| 专用链接资源类型/子资源 |专用 DNS 区域名称 | 公共 DNS 区域转发器 |
|---|---|---|---|
| SQL DB (Microsoft.Sql/servers) / Sql Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers) / Sql Server  | privatelink.database.windows.net | database.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts) / Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts) / Table (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts) / Queue (queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts) / File (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts) / Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake 文件系统 Gen2 (Microsoft.Storage/storageAccounts) / Data Lake 文件系统 Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL - 单一服务器 (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / vault | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes 服务 - Kubernetes API (Microsoft.ContainerService/managedClusters) / managedCluster | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure 搜索 (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Azure 容器注册表 (Microsoft.ContainerRegistry/registries) / registry | privatelink.azurecr.io | azurecr.io |
| Azure 应用程序配置 (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure 备份 (Microsoft.RecoveryServices/vaults) / vault | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure 事件中心 (Microsoft.EventHub/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 服务总线 (Microsoft.ServiceBus/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 中继 (Microsoft.Relay/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 事件网格 (Microsoft.EventGrid/topics) / topic | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure 事件网格 (Microsoft.EventGrid/domains) / domain | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure WebApps (Microsoft.Web/sites) / site | privatelink.azurewebsites.net | azurewebsites.net |
| Azure 机器学习 (Microsoft.MachineLearningServices/workspaces) / workspace | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>DNS 配置方案

服务的 FQDN 自动解析为公共 IP 地址，因此，若要解析为专用终结点的专用 IP 地址，必须相应地更改 DNS 配置。

DNS 以正确的方式解析专用终结点 IP 地址，是使应用程序正常工作的一个关键组件。

根据你的偏好，以下方案适用于集成的 DNS 解析：

- [不带自定义 DNS 服务器的虚拟网络工作负荷](#virtual-network-workloads-without-custom-dns-server)
- [使用 DNS 转发器的本地工作负荷](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>不带自定义 DNS 服务器的虚拟网络工作负荷

此配置适用于不带自定义 DNS 服务器的虚拟网络工作负荷。 在这种方案中，客户端会将专用终结点 IP 地址查询到 Azure 提供的 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)。 Azure DNS 将负责专用 DNS 区域的 DNS 解析。


> [!NOTE]
> 这种方案使用 Azure SQL 数据库建议的专用 DNS 区域。 对于其他服务，可以使用以下参考 [Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)来调整模型。

要正确配置，需要以下资源：

- 客户端虚拟网络

- 有[类型 A 记录](../dns/dns-zones-records.md#record-types)的专用 DNS 区域 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)

- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图显示了使用专用 DNS 区域的虚拟网络工作负荷中的 DNS 解析顺序

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="单个虚拟网络和 Azure 提供的 DNS":::

此模型可扩展到与同一专用终结点相关联的多个对等虚拟网络。 可通过向所有对等虚拟网络的专用 DNS 区域[添加新的虚拟网络链接](../dns/private-dns-virtual-network-links.md)来实现此操作。

> [!IMPORTANT]
> 此配置需要单个专用 DNS 区域，要为不同的虚拟网络创建具有相同名称的多个区域，需要手动操作来合并 DNS 记录

在此方案中，存在一个[中心与分支](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)网络拓扑，分支网络共享一个专用终结点，所有分支虚拟网络链接到同一专用 DNS 区域。 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="具有 Azure 提供的 DNS 的中心和分支":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>使用 DNS 转发器的本地工作负荷

若要使本地工作负荷能够将专用终结点的 FQDN 解析为专用 IP 地址，必须使用 DNS 转发器来解析部署在 Azure 中的 Azure 服务[公共 DNS 区域](#azure-services-dns-zone-configuration)。


以下方案适用于在 Azure 中具有 DNS 转发器的本地网络，而该转发器又负责通过服务器级别转发器将所有 DNS 查询解析为 Azure 提供的 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> 这种方案使用 Azure SQL 数据库建议的专用 DNS 区域。 对于其他服务，可以使用以下参考  [Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)来调整模型。

要正确配置，需要以下资源：

- 本地网络
-  [连接到本地](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)的虚拟网络
- 部署在 Azure 中的 DNS 转发器 
- 有 [类型 A 记录](../dns/dns-zones-records.md#record-types)的专用 DNS 区域 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) 
- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图显示了一个本地网络的 DNS 解析序列，该本地网络使用部署在 Azure 中的 DNS 转发器，而解析由[链接到虚拟网络](../dns/private-dns-virtual-network-links.md)的专用 DNS 区域进行。

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="使用 Azure DNS 的本地网络":::

可以为已有 DNS 解决方案的本地网络扩展此配置。 
需要配置本地 DNS 解决方案，使其通过引用部署在 Azure 中的 DNS 转发器的 [条件转发器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)将 DNS 流量转发到 Azure DNS。

> [!NOTE]
> 这种方案使用 Azure SQL 数据库建议的专用 DNS 区域。 对于其他服务，可以使用以下参考  [Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)来调整模型。

要正确配置，需要以下资源：

- 具有自定义 DNS 解决方案的本地网络 
-  [连接到本地](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)的虚拟网络
- 部署在 Azure 中的 DNS 转发器
- 有 [类型 A 记录](../dns/dns-zones-records.md#record-types)的专用 DNS 区域 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  
- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图显示了一个本地网络的 DNS 解析序列，该本地网络将 DNS 流量条件转发到 Azure，而解析由 [链接到虚拟网络](../dns/private-dns-virtual-network-links.md)的专用 DNS 区域进行。

> [!IMPORTANT]
> 条件转发必须指向建议的[公共 DNS 区域转发器](#azure-services-dns-zone-configuration) （例如  `database.windows.net`） 而不是privatelink **** .database.windows.net

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="本地转发到 Azure DNS":::


## <a name="next-steps"></a>后续步骤
- [了解专用终结点](private-endpoint-overview.md)
