---
title: Contoso 迁移系列 |Microsoft Docs
description: 提供 Contoso 用于将其本地数据中心迁移到 Azure 的迁移策略和方案的概述。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: raynew
ms.openlocfilehash: d20c0be47f44cdce8ea895007494565d37f5923f
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179138"
---
# <a name="contoso-migration-series"></a>Contoso 迁移系列


我们有一系列文章演示了虚构的组织 Contoso 如何将本地基础结构迁移到[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)云。 

该系列包括相关信息和方案来说明如何设置基础结构的迁移及运行不同类型的迁移。 方案在执行过程中的复杂性会增长。 文章展示 Contoso 公司如何完成其迁移任务，但始终提供一般阅读和特定说明的指针。

## <a name="migration-articles"></a>迁移文章

下表总结了此系列中的文章。  

- 每个迁移方案均由决定迁移策略的略有不同的业务目标所驱动。
- 对于每个部署方案，我们提供有关业务驱动因素和目标、建议的体系结构、执行迁移的步骤、迁移完成后的清理建议和下一步骤的信息。

**文章** | **详细信息** 
--- | --- 
[文章 1：概述](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 概述文章系列、Contoso 的迁移策略和该系列所用的示例应用。 
[文章 2：部署 Azure 基础结构](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso 准备其本地和 Azure 基础结构进行迁移。 该系列的所有迁移文章共用同一个基础结构。 
[文章 3：评估要迁移到 Azure 的本地资源](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso 评估 VMware 上运行的本地 SmartHotel360 应用。 Contoso 使用 Azure Migrate 服务评估应用 VM，并使用数据迁移助手评估应用 SQL Server 数据库。
[文章 4：在 Azure VM 和 SQL 数据库托管实例上重新托管应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso 将其本地 SmartHotel360 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM。 Contoso 使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 Azure SQL 数据库托管实例。
[文章 5：在 Azure VM 上重新托管应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso 使用 Site Recovery 服务将其 SmartHotel360 应用 VM 迁移到 Azure VM。 
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组中重新托管应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso 迁移 SmartHotel360 应用。 Contoso 使用 Site Recovery 迁移应用 VM。 它使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 
[文章 7：在 Azure VM 上重新托管 Linux 应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso 使用 Site Recovery 服务将其 Linux osTicket 应用直接迁移到 Azure VM。
[文章 8：在 Azure VM 和 Azure Database for MySQL 上重新托管 Linux 应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso 使用 Site Recovery 将其 Linux osTicket 应用迁移到 Azure VM。 它使用 MySQL Workbench 将应用数据库迁移到 Azure Database for MySQL。 
[文章 9：在 Azure Web 应用和 Azure SQL 数据库中重构应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso 将其 SmartHotel360 应用迁移到 Azure Web 应用，并使用数据库迁移助手将应用数据库迁移到 Azure SQL Server 实例。     
[文章 10：在 Azure Web 应用和 Azure Database for MySQL 中重构 Linux 应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso 使用 Azure 流量管理器将其 Linux osTicket 应用迁移到多个 Azure 区域上的 Azure Web 应用，并与 GitHub 集成以实现持续交付。 Contoso 将应用数据库迁移到 Azure Database for MySQL 实例。 
[文章 11：在 Azure DevOps Services 中重构 Team Foundation Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso 将其本地 Team Foundation Server 部署迁移到 Azure 中的 Azure DevOps Services。
[文章 12：在 Azure 容器和 Azure SQL 数据库中重构应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso 将其 SmartHotel 应用迁移到 Azure。 然后，它将应用 Web 层重新架构为 Azure Service Fabric 中运行的 Windows 容器，以及具有 Azure SQL 数据库的数据库。
[文章 13：在 Azure 中重新生成应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso 使用一系列 Azure 功能和服务（包括 Azure 应用服务、Azure Kubernetes 服务 (AKS)、Azure Functions、Azure 认知服务和 Azure Cosmos DB）重新生成其 SmartHotel 应用。
[文章 14：到 Azure 的大规模迁移](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | 尝试过组合迁移后，Contoso 准备大规模整体迁移到 Azure。

## <a name="next-steps"></a>后续步骤

- [了解](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/)云迁移。
- 了解有关[数据库迁移指南](https://datamigration.microsoft.com/)中的其他方案（源/目标对）的迁移策略。
