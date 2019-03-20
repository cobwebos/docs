---
title: 使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑 | Microsoft Docs
description: 了解数据库迁移服务的源和目标配置。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: d12d6b1274a756bfb13761ab999a1539bcee3657
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58178112"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑
本文介绍 Azure SQL 数据库迁移服务可使用的各种网络拓扑，以提供从本地 SQL Server 到 Azure SQL 数据库托管实例的全面迁移体验。

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>为混合工作负载配置的 Azure SQL 数据库托管实例 
如果 Azure SQL 数据库托管实例与本地网络连接，请使用此拓扑。 此方法提供最简化的网络路由，并在迁移过程中提供最大数据吞吐量。

![混合工作负载的网络拓扑](media/resource-network-topologies/hybrid-workloads.png)

**要求**
- 在此方案中，Azure SQL 数据库托管实例和 Azure 数据库迁移服务实例都创建在同一 Azure VNET 中，但他们使用不同的子网。  
- 本方案中使用的 VNET 还通过使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 与本地网络连接。

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL 数据库托管实例与本地网络隔离
如果环境要求以下的一种或多种方案，则使用此网络拓扑：
- Azure SQL 数据库托管实例与本地连接分离，但是 Azure 数据库迁移服务实例与本地网络连接。
- 如果基于角色的访问控制 (RBAC) 策略已到位，而且你需要限制用户对托管 Azure SQL 数据库托管实例的同一订阅的访问权限。
- 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 VNET 在不同的订阅中。

![托管实例的网络拓扑与本地网络分离](media/resource-network-topologies/mi-isolated-workload.png)

**要求**
- Azure 数据库迁移服务针对本方案使用的 VNET 还需通过使用 https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 连接到本地网络。
- 在用于 Azure SQL 数据库托管实例的 VNET 和 Azure 数据库迁移服务之间设置 [VNET 网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>云到云的迁移：共享的 VNET

如果源 SQL Server 托管在 Azure VM 中，且与 Azure SQL 数据库托管实例以及 Azure 数据库迁移服务共享同一个 VNET，请使用此拓扑。

![共享 VNET 的云到云迁移的网络拓扑](media/resource-network-topologies/cloud-to-cloud.png)

**要求**
- 没有其他要求。

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>云到云的迁移：独立的 VNET

如果环境要求以下的一种或多种方案，则使用此网络拓扑：
- Azure SQL 数据库托管实例所在的 VNET 是独立的。
- 如果基于角色的访问控制 (RBAC) 策略已到位，而且你需要限制用户对托管 Azure SQL 数据库托管实例的同一订阅的访问权限。
- 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 VNET 在不同的订阅中。

![独立 VNET 的云到云迁移的网络拓扑](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**要求**
- 在用于 Azure SQL 数据库托管实例的 VNET 和 Azure 数据库迁移服务之间设置 [VNET 网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。

## <a name="inbound-security-rules"></a>入站安全规则

| **NAME**   | **PORT** | **PROTOCOL** | **源** | **DESTINATION** | **ACTION** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 任意      | 任意          | DMS SUBNET | 任意             | 允许      |

## <a name="outbound-security-rules"></a>入站安全规则

| **NAME**                  | **PORT**                                              | **PROTOCOL** | **源** | **DESTINATION**           | **ACTION** | **规则的原因**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 管理                | 443,9354                                              | TCP          | 任意        | 任意                       | 允许      | 通过服务总线和 Azure blob 存储进行管理平面通信。 <br/>（如果启用了 Microsoft 对等互连，可能不需要此规则。）                                                             |
| 诊断               | 12000                                                 | TCP          | 任意        | 任意                       | 允许      | DMS 使用此规则收集诊断信息以进行故障排除。                                                                                                                      |
| SQL 源服务器         | 1433（或 SQL Server 正在侦听的 TCP IP 端口） | TCP          | 任意        | 本地地址空间 | 允许      | 来自 DMS 的 SQL Server 源连接 <br/>（如果使用站点到站点连接，则可能不需要此规则。）                                                                                       |
| SQL Server 命名实例 | 1434                                                  | UDP          | 任意        | 本地地址空间 | 允许      | 来自 DMS 的 SQL Server 命名实例源连接 <br/>（如果使用站点到站点连接，则可能不需要此规则。）                                                                        |
| SMB 共享                 | 445                                                   | TCP          | 任意        | 本地地址空间 | 允许      | DMS 的 SMB 网络共享用于存储数据库备份文件，以便迁移到 Azure VM 上的 Azure SQL 数据库 MI 和 SQL Server <br/>（如果使用站点到站点连接，则可能不需要此规则）。 |
| DMS_subnet                | 任意                                                   | 任意          | 任意        | DMS_Subnet                | 允许      |                                                                                                                                                                                                  |

## <a name="see-also"></a>另请参阅
- [将 SQL Server 迁移到 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [使用 Azure 数据库迁移服务的先决条件概述](https://docs.microsoft.com/azure/dms/pre-reqs)
- [使用 Azure 门户创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>后续步骤
- 有关 Azure 数据库迁移服务的概述，请参阅[什么是 Azure 数据库迁移服务？](dms-overview.md)一文。
- 有关 Azure 数据库迁移服务区域可用性的当前信息，请参阅[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) 页面。