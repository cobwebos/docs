---
title: 用于 SQL 托管实例迁移的网络拓扑
titleSuffix: Azure Database Migration Service
description: 了解 Azure SQL 数据库托管实例使用 Azure 数据库迁移服务进行迁移的源和目标配置。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: db875ea099b0093bf1d43bd64b1ae4c07db05b45
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437719"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>使用 Azure 数据库迁移服务托管实例迁移的 Azure SQL DB 网络拓扑

本文介绍 Azure 数据库迁移服务可使用的各种网络拓扑，以提供从本地 SQL Server 到 Azure SQL 数据库托管实例的综合迁移体验。

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>为混合工作负载配置的 Azure SQL 数据库托管实例 

如果 Azure SQL 数据库托管实例与本地网络连接，请使用此拓扑。 此方法提供最简化的网络路由，并在迁移过程中提供最大数据吞吐量。

![混合工作负载的网络拓扑](media/resource-network-topologies/hybrid-workloads.png)

**要求**

- 在此方案中，Azure SQL 数据库托管实例和 Azure 数据库迁移服务实例在同一 Azure VNet 中创建，但它们使用不同的子网。  
- 此方案中使用的 VNet 也使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)连接到本地网络。

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL 数据库托管实例与本地网络隔离

如果环境要求以下的一种或多种方案，则使用此网络拓扑：

- Azure SQL 数据库托管实例与本地连接隔离，但 Azure 数据库迁移服务实例已连接到本地网络。
- 如果基于角色的访问控制（RBAC）策略已准备就绪，并且你需要限制用户访问承载 Azure SQL 数据库托管实例的同一订阅。
- 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 Vnet 在不同的订阅中。

![托管实例的网络拓扑与本地网络分离](media/resource-network-topologies/mi-isolated-workload.png)

**要求**

- Azure 数据库迁移服务用于此方案的 VNet 还必须使用（ https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)）连接到本地网络。
- 在用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 VNet 之间设置[vnet 网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>云到云的迁移：共享 VNet

如果源 SQL Server 托管在 Azure VM 中，并与 Azure SQL 数据库托管实例和 Azure 数据库迁移服务共享同一 VNET，请使用此拓扑。

![使用共享 VNet 进行云到云迁移的网络拓扑](media/resource-network-topologies/cloud-to-cloud.png)

**要求**

- 没有其他要求。

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>云到云的迁移：独立 VNet

如果环境要求以下的一种或多种方案，则使用此网络拓扑：

- 在独立的 VNet 中预配 Azure SQL 数据库托管实例。
- 如果基于角色的访问控制（RBAC）策略已准备就绪，并且你需要限制用户访问承载 Azure SQL 数据库托管实例的同一订阅。
- 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 Vnet 在不同的订阅中。

![具有独立 VNet 的云到云迁移的网络拓扑](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**要求**

- 在用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 VNet 之间设置[vnet 网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。

## <a name="inbound-security-rules"></a>入站安全规则

| **NAME**   | **PORT** | **PROTOCOL** | **源** | **DESTINATION** | **ACTION** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Any      | Any          | DMS SUBNET | Any             | 允许      |

## <a name="outbound-security-rules"></a>入站安全规则

| **NAME**                  | **PORT**                                              | **PROTOCOL** | **源** | **DESTINATION**           | **ACTION** | **规则的原因**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 管理                | 443,9354                                              | TCP          | Any        | Any                       | 允许      | 通过服务总线和 Azure blob 存储进行管理平面通信。 <br/>（如果启用了 Microsoft 对等互连，可能不需要此规则。）                                                             |
| 诊断               | 12000                                                 | TCP          | Any        | Any                       | 允许      | DMS 使用此规则收集诊断信息以进行故障排除。                                                                                                                      |
| SQL 源服务器         | 1433（或 SQL Server 正在侦听的 TCP IP 端口） | TCP          | Any        | 本地地址空间 | 允许      | 来自 DMS 的 SQL Server 源连接 <br/>（如果使用站点到站点连接，则可能不需要此规则。）                                                                                       |
| SQL Server 命名实例 | 1434                                                  | UDP          | Any        | 本地地址空间 | 允许      | 来自 DMS 的 SQL Server 命名实例源连接 <br/>（如果使用站点到站点连接，则可能不需要此规则。）                                                                        |
| SMB 共享                 | 445                                                   | TCP          | Any        | 本地地址空间 | 允许      | DMS 的 SMB 网络共享用于存储数据库备份文件，以便迁移到 Azure VM 上的 Azure SQL 数据库 MI 和 SQL Server <br/>（如果使用站点到站点连接，则可能不需要此规则）。 |
| DMS_subnet                | Any                                                   | Any          | Any        | DMS_Subnet                | 允许      |                                                                                                                                                                                                  |

## <a name="see-also"></a>另请参阅

- [将 SQL Server 迁移到 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [使用 Azure 数据库迁移服务的先决条件概述](https://docs.microsoft.com/azure/dms/pre-reqs)
- [使用 Azure 门户创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>后续步骤

- 有关 Azure 数据库迁移服务的概述，请参阅[什么是 Azure 数据库迁移服务？](dms-overview.md)一文。
- 有关 Azure 数据库迁移服务的区域可用性的最新信息，请参阅 "[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)" 页。
