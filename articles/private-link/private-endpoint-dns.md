---
title: Azure 专用终结点 DNS 配置
description: 了解 Azure 专用终结点 DNS 配置
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7db02546b562f1b542080efdbda8968940655e95
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121267"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure 专用终结点 DNS 配置


使用完全限定的域名 (FQDN) 作为连接字符串的一部分连接到专用链接资源时，必须正确配置 DNS 设置，以解析为分配的专用 IP 地址。 现有的 Azure 服务可能已有在通过公共终结点进行连接时要使用的 DNS 配置。 需要重写此配置才能使用专用终结点进行连接。 
 
与专用终结点关联的网络接口包含配置 DNS 所需的完整信息，其中包括为给定专用链接资源分配的 FQDN 和专用 IP 地址。 
 
可使用以下选项来配置专用终结点的 DNS 设置： 
- **使用主机文件（仅建议用于测试）**。 可以使用虚拟机上的主机文件来替代 DNS。  
- **使用专用 DNS 区域**。 可以使用[专用 DNS 区域](../dns/private-dns-privatednszone.md)替代给定专用终结点的 DNS 解析。 可将专用 DNS 区域链接到虚拟网络，以解析特定的域。
- **使用 DNS 转发器（可选）**。 可以使用 DNS 转发器替代给定专用链接资源的 DNS 解析。 如果 [DNS 服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)托管在虚拟网络上，可以创建 DNS 转发规则，以使用专用 DNS 区域来简化所有专用链接资源的配置。
 
> [!IMPORTANT]
> 不建议替代正在用于解析公共终结点的区域。 在不 DNS 转发到公共 DNS 的情况下，与资源的连接无法正确解析。 若要避免出现问题，请创建不同的域名，或对以下每个服务采用建议的名称。 

## <a name="azure-services-dns-zone-configuration"></a>Azure 服务 DNS 区域配置
Azure 服务将在公共 DNS 上创建一个规范名称 DNS 记录（CNAME），以将解析重定向到建议的专用域名。 可以用专用终结点的专用 IP 地址替代解析。 
 
应用程序无需更改连接 URL。 尝试使用公共 DNS 进行解析时，DNS 服务器现将解析为专用终结点。 此过程不会影响现有应用程序。 

对于 Azure 服务，请根据下表中所述使用建议的区域名称：

| Private Link 资源类型/Subresource |专用 DNS 区域名称 | 公共 DNS 区域名称 |
|---|---|---|---|
| SQL DB （Sql Server）/Sql Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics （Sql Server）/Sql Server  | privatelink.database.windows.net | database.windows.net |
| 存储帐户（storageAccounts/）/Blob （blob、blob_secondary） | privatelink.blob.core.windows.net | blob.core.windows.net |
| 存储帐户（storageAccounts/）/表（表、table_secondary） | privatelink.table.core.windows.net | table.core.windows.net |
| 存储帐户（storageAccounts/）/队列（队列、queue_secondary） | privatelink.queue.core.windows.net | queue.core.windows.net |
| 存储帐户（storageAccounts/）/文件（文件、file_secondary） | privatelink.file.core.windows.net | file.core.windows.net |
| 存储帐户（storageAccounts/）/Web （web、web_secondary） | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake 文件系统 Gen2 （Gen2/storageAccounts）/Data Lake File System （dfs，dfs_secondary） | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB （AzureCosmosDB/databaseAccounts）/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB （AzureCosmosDB/databaseAccounts）/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB （AzureCosmosDB/databaseAccounts）/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB （AzureCosmosDB/databaseAccounts）/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB （AzureCosmosDB/databaseAccounts）/Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL-单服务器（DBforPostgreSQL/服务器）/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL （DBforMySQL/服务器）/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB （DBforMariaDB/服务器）/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault （KeyVault/保管库）/保管库 | privatelink.vaultcore.azure.net | vault.azure.net |
| Azure Kubernetes 服务-Kubernetes API （ContainerService/managedClusters）/managedCluster | privatelink.{region}. azmk8s | {region}. azmk8s |
| Azure 搜索（searchServices/）/searchService | privatelink.search.windows.net | search.windows.net |
| Azure 容器注册表（Microsoft.containerregistry/注册表）/注册表 | privatelink.azurecr.io | azurecr.io |
| Azure 应用配置（AppConfiguration/configurationStores）/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure 备份（Microsoft.recoveryservices/保管库）/保管库 | privatelink.{region}. windowsazure.storage | {region}. windowsazure.storage |
| Azure 事件中心（node.js/命名空间）/命名空间 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 服务总线（node.js/命名空间）/命名空间 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 中继（Microsoft 中继/命名空间）/命名空间 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 事件网格（EventGrid/主题）/主题 | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure 事件网格（EventGrid/域）/域 | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure WebApps （Microsoft 网站/站点）/站点 | privatelink.azurewebsites.net | azurewebsites.net |
| Azure 机器学习（MachineLearningServices/工作区）/工作区 | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>DNS 配置方案

服务的 FQDN 自动解析为公共 IP 地址，因此，若要解析到专用终结点的专用 IP 地址，你必须相应地更改 DNS 配置。

DNS 是一项关键的组件，可通过正确的方式解析专用终结点 IP 地址来使应用程序正常工作。

根据你的喜好，以下方案适用于集成的 DNS 解析：

- [不带自定义 DNS 服务器的虚拟网络工作负荷](#virtual-network-workloads-without-custom-dns-server)
- [使用 DNS 转发器的本地工作负荷](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>不带自定义 DNS 服务器的虚拟网络工作负荷

此配置适用于没有自定义 DNS 服务器的虚拟网络工作负荷。 在此方案中，客户端会查询 Azure 提供的 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)的专用终结点 IP 地址。 Azure DNS 将负责专用 DNS 区域的 DNS 解析。


> [!NOTE]
> 这种情况下，建议使用 Azure SQL 数据库专用 DNS 区域。 对于其他服务，可以使用以下参考[Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)来调整模型。

若要正确配置，需要以下资源：

- 客户端虚拟网络

- 使用[类型 A 记录](../dns/dns-zones-records.md#record-types)专用 DNS 区域[privatelink.database.windows.net](../dns/private-dns-privatednszone.md)

- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图说明了使用专用 DNS 区域的虚拟网络工作负载中的 DNS 解析顺序

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="单个虚拟网络和 Azure 提供的 DNS":::

此模型可扩展到多个与同一专用终结点相关联的对等互连虚拟网络。 为此，可[将新的虚拟网络链接添加](../dns/private-dns-virtual-network-links.md)到所有对等互连虚拟网络的专用 DNS 区域。

> [!IMPORTANT]
>  此配置需要单个专用 DNS 区域，为不同的虚拟网络创建具有相同名称的多个区域需要手动操作来合并 DNS 记录

在此方案中，有一个[集线器 & 辐射](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)网络拓扑，其中包含共享公共专用终结点的辐射网络，所有辐射虚拟网络都链接到同一专用 dns 区域。 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="具有 Azure 提供的 DNS 的中心和辐射":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>使用 DNS 转发器的本地工作负荷
 
要使本地工作负荷能够将专用终结点的 FQDN 解析为专用 IP 地址，必须使用 DNS 转发器来解决 Azure 中部署的 Azure 服务[公共 DNS 区域](#azure-services-dns-zone-configuration)。


以下方案适用于在 Azure 中具有 DNS 转发器的本地网络，后者负责将所有 DNS 查询通过服务器级别转发器解析到 Azure 提供的 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> 这种情况下，建议使用 Azure SQL 数据库专用 DNS 区域。对于其他服务，可以使用以下参考 [Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)来调整模型。

若要正确配置，需要以下资源：

- 本地网络
- 虚拟网络 [已连接到本地](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Azure 中部署的 DNS 转发器 
-  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)   使用 [类型 A 记录](../dns/dns-zones-records.md#record-types)专用 DNS 区域 privatelink.database.windows.net
- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图显示了使用 Azure 中部署的 DNS 转发器的本地网络中的 DNS 解析序列，其中的解决方案是通过链接到虚拟网络的专用 DNS 区域进行的。

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="使用 Azure DNS 本地":::

可以为已有 DNS 解决方案的本地网络扩展此配置。 
需要配置本地 DNS 解决方案，以便通过引用 Azure 中部署的 DNS 转发器的 [条件转发器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)，将 dns 流量转发到 Azure DNS。

> [!NOTE]
> 这种情况下，建议使用 Azure SQL 数据库专用 DNS 区域。对于其他服务，可以使用以下参考 [Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)来调整模型。

若要正确配置，需要以下资源：


- 使用自定义 DNS 解决方案的本地网络 
- 虚拟网络 [已连接到本地](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Azure 中部署的 DNS 转发器
-  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)    使用 [类型 A 记录](../dns/dns-zones-records.md#record-types)专用 DNS 区域 privatelink.database.windows.net
- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图说明了本地网络中的 DNS 解析序列，该序列有条件地将 DNS 流量转发到 Azure，在该序列中，通过专用 DNS 区域链接到虚拟网络进行解析。

> [!IMPORTANT]
> 必须对 [公共 DNS 区域](#azure-services-dns-zone-configuration)（例如：）执行条件性转发    `database.windows.net`   ，而不是 **privatelink**。 database.windows.net

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="本地转发到 Azure DNS":::


## <a name="next-steps"></a>后续步骤
- [了解专用终结点](private-endpoint-overview.md)
