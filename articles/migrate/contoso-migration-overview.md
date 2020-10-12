---
title: Contoso 迁移系列 |Microsoft Docs
description: 用于迁移到 Azure 的 Contoso 示例迁移方案的链接。
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: f9f7b3b64cf91019ed4e40d5d52b859419b74836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86107625"
---
# <a name="contoso-migration-series"></a>Contoso 迁移系列


我们有一系列文章演示了虚拟组织 Contoso 如何将其本地基础结构迁移到 [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 云。 

此系列包括的方案说明了如何设置基础结构迁移，以及如何运行不同类型的迁移。 方案在执行过程中的复杂性会增长。 本文说明了 Contoso 公司如何处理迁移，但在整个过程中提供了一般说明和指针。

## <a name="migration-articles"></a>迁移文章

下表总结了此系列中的文章。  

- 每个迁移方案都是由一种略有不同的业务要求来决定迁移策略。
- 对于每个部署方案，我们提供有关业务驱动程序和目标、建议的体系结构、执行迁移的步骤以及迁移完成后的清除和后续步骤的建议的信息。


**文章** | **详细信息** 
--- | --- 
[第 1 篇：概述](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 概述文章系列、Contoso 的迁移策略和该系列所用的示例应用。 
[文章 2：部署 Azure 基础结构](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso 准备了其本地基础结构和用于迁移的 Azure 基础结构。 该系列的所有文章共用同一个基础结构。 
[文章 3：评估要迁移到 Azure 的本地资源](/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso 评估 VMware 上运行的本地 SmartHotel360 应用。 Contoso 使用 Azure Migrate 服务评估应用 VM，并使用数据迁移助手评估应用 SQL Server 数据库。
[文章4：在 Azure VM 和 SQL 托管实例上 Rehost 应用](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso 将其本地 SmartHotel360 应用直接迁移到 Azure。 Contoso 使用 [Azure Migrate](./migrate-services-overview.md)迁移应用前端 VM。 Contoso 使用 [Azure 数据库迁移服务](../dms/dms-overview.md)将应用数据库迁移到 SQL 托管实例。
[文章 5：在 Azure VM 上重新托管应用](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso 使用 Azure Migrate 服务将其 SmartHotel360 应用 Vm 迁移到 Azure Vm。 
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组中重新托管应用](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso 迁移 SmartHotel360 应用。 Contoso 使用 Azure Migrate 来迁移应用程序 Vm。 它使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 
[文章 7：将 Linux 应用重新托管到 Azure VM](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso 使用 Azure Migrate 完成将其 Linux osTicket 应用程序迁移到 Azure Vm 的过程。
[文章 8：在 Azure VM 和 Azure Database for MySQL 上重新托管 Linux 应用](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso 使用 Azure Migrate 将其 Linux osTicket 应用迁移到 Azure Vm。 使用 Azure 数据库迁移服务 (将应用数据库迁移到 Azure Database for MySQ，其中包含使用 MySQL 工作台) 的替代选项。
[文章 9：在 Azure Web 应用和 Azure SQL 数据库中重构应用](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso 使用 Azure 数据库迁移服务将其 SmartHotel360 应用迁移到 Azure web 应用，并将应用数据库迁移到 Azure SQL 数据库。
[文章10：使用 Azure 应用 Services 和 SQL 托管实例重构 Windows 应用](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso 使用 Azure 数据库迁移服务将基于 Windows 的本地应用程序迁移到 Azure web 应用，并将应用程序数据库迁移到 Azure SQL 托管实例。
[文章11：在 Azure web 应用中重构 Linux 应用并 Azure Database for MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso 使用 Azure 流量管理器将其 Linux osTicket 应用迁移到多个 Azure 区域中的 Azure web 应用，以实现持续交付。 Contoso 将应用数据库迁移到 Azure Database for MySQL 实例。 
[文章12：重构 Azure DevOps Services 上的 Team Foundation Server](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso 将其本地 Team Foundation Server 部署迁移到 Azure 中的 Azure DevOps Services。
[文章 13：在 Azure 中重新生成应用](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso 使用一系列 Azure 功能和服务重新构建其 SmartHotel 应用，其中包括 Azure App Service、Azure Kubernetes Service (AKS) 、Azure Functions、Azure 认知服务和 Azure Cosmos DB。
[文章 14：到 Azure 的大规模迁移](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | 尝试过组合迁移后，Contoso 准备大规模整体迁移到 Azure。



## <a name="next-steps"></a>后续步骤

- [了解](/azure/architecture/cloud-adoption/migrate/) 云迁移。
- 了解有关 [数据库迁移指南](https://datamigration.microsoft.com/)中)  (源/目标对的其他方案的迁移策略。
