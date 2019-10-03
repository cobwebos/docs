---
title: 网络拓扑使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例 |Microsoft Docs
description: 了解 Azure 数据库迁移服务的源和目标配置。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: 74613599903f7cde606295a1e2d9eaaa0924cf50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808421"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑

本文讨论了各种 Azure 数据库迁移服务可以使用来提供全面的迁移体验从本地 SQL 服务器到 Azure SQL 数据库托管实例的网络拓扑。

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>为混合工作负载配置的 Azure SQL 数据库托管实例 

如果 Azure SQL 数据库托管实例与本地网络连接，请使用此拓扑。 此方法提供最简化的网络路由，并在迁移过程中提供最大数据吞吐量。

![混合工作负载的网络拓扑](media/resource-network-topologies/hybrid-workloads.png)

**要求**

- 在此方案中，在相同的 Azure VNet 中创建 Azure SQL 数据库托管实例和 Azure 数据库迁移服务实例，但它们使用不同的子网。  
- 此方案中使用的 VNet 还通过使用连接到本地网络[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL 数据库托管实例与本地网络隔离

如果环境要求以下的一种或多种方案，则使用此网络拓扑：

- Azure SQL 数据库托管的实例都独立于本地连接，但你的 Azure 数据库迁移服务实例连接到本地网络。
- 如果角色基于访问控制 (RBAC) 策略已到位，你需要限制用户对访问 Azure SQL 数据库托管的实例的宿主的同一个订阅。
- 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 Vnet 属于不同订阅。

![托管实例的网络拓扑与本地网络分离](media/resource-network-topologies/mi-isolated-workload.png)

**要求**

- Azure 数据库迁移服务针对本方案使用的 VNet 必须还连接到本地网络使用 (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。
- 设置[VNet 网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)之间 VNet 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务。

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>云到云迁移： 共享的 VNet

如果源 SQL Server Azure VM 中托管，并且与 Azure SQL 数据库托管实例和 Azure 数据库迁移服务共享同一个 VNET，请使用此拓扑。

![使用共享 VNet 的云到云迁移的网络拓扑](media/resource-network-topologies/cloud-to-cloud.png)

**要求**

- 没有其他要求。

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>云到云的迁移： 独立的 VNet

如果环境要求以下的一种或多种方案，则使用此网络拓扑：

- Azure SQL 数据库托管的实例 VNet 是独立的预配。
- 如果角色基于访问控制 (RBAC) 策略已到位，你需要限制用户对访问 Azure SQL 数据库托管的实例的宿主的同一个订阅。
- 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 Vnet 属于不同订阅。

![使用 VNet 是独立的云到云迁移的网络拓扑](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**要求**

- 设置[VNet 网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)之间 VNet 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务。

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

- Azure 数据库迁移服务的概述，请参阅文章[什么是 Azure 数据库迁移服务？](dms-overview.md)。
- Azure 数据库迁移服务的区域可用性的当前信息，请参阅[区域的可用产品](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)页。
