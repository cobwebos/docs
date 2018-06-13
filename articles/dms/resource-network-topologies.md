---
title: 使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑 | Microsoft Docs
description: 了解数据库迁移服务的源和目标配置。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 5904864ffba656dab17e1549ed9832be4258a67f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
ms.locfileid: "30235394"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑
本文将介绍 Azure SQL 数据库迁移服务可使用的各种网络拓扑，以提供从本地 SQL 服务器到 Azure SQL 数据库托管实例的无缝迁移体验。

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>为混合工作负载配置的 Azure SQL 数据库托管实例 
如果 Azure SQL 数据库托管实例与本地网络连接，请使用此拓扑。 此方法提供最简化的网络路由，并在迁移过程中提供最大数据吞吐量。

![混合工作负载的网络拓扑](media\resource-network-topologies\hybrid-workloads.png)

**要求**
- 在此方案中，Azure SQL 数据库托管实例和 Azure 数据库迁移服务实例都创建在同一 Azure VNET 中，但他们使用不同的子网。  
- 本方案中使用的 VNET 还通过使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 与本地网络连接。

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL 数据库托管实例与本地网络隔离
如果环境要求以下的一种或多种方案，则使用此网络拓扑：
- Azure SQL 数据库托管实例与本地连接分离，但是 Azure 数据库迁移服务实例与本地网络连接。
- 如果基于角色的访问控制 (RBAC) 策略已到位，你需要限制用户对托管 Azure SQL 数据库托管实例的同一订阅的访问权限。
- 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 VNET 在不同的订阅中。

![托管实例的网络拓扑与本地网络分离](media\resource-network-topologies\mi-isolated-workload.png)

**要求**
- Azure 数据库迁移服务针对本方案使用的 VNET 还需通过使用 https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 连接到本地网络。
- 需要在用于 Azure SQL 数据库托管实例的 VNET 和 Azure 数据库迁移服务之间创建 [VNET 网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>云到云的迁移：共享的 VNET

如果源 SQL Server 托管在 Azure VM 中，且与 Azure SQL 数据库托管实例以及 Azure 数据库迁移服务共享同一个 VNET，请使用此拓扑。

![共享 VNET 的云到云迁移的网络拓扑](media\resource-network-topologies\cloud-to-cloud.png)

**要求**
- 没有其他要求。

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>云到云的迁移：独立的 VNET

如果环境要求以下的一种或多种方案，则使用此网络拓扑：
- Azure SQL 数据库托管实例所在的 VNET 是独立的。
- 如果基于角色的访问控制 (RBAC) 策略已到位，你需要限制用户对托管 Azure SQL 数据库托管实例的同一订阅的访问权限。
- 用于 Azure SQL 数据库托管实例和 Azure 数据库迁移服务的 VNET 在不同的订阅中。

![独立 VNET 的云到云迁移的网络拓扑](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**要求**
- 需要在用于 Azure SQL 数据库托管实例的 VNET 和 Azure 数据库迁移服务之间创建 [VNET 网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="see-also"></a>另请参阅
- [将 SQL Server 迁移到 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [使用 Azure 数据库迁移服务的先决条件概述](https://docs.microsoft.com/azure/dms/pre-reqs)
- [使用 Azure 门户创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>后续步骤
有关公共预览版期间 Azure 数据库迁移服务和区域可用性的概述，请参阅[什么是 Azure 数据库迁移服务预览版](dms-overview.md)一文。 