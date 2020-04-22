---
title: Contoso 迁移系列 |微软文档
description: 链接到 Contoso 示例迁移方案，用于迁移到 Azure。
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676329"
---
# <a name="contoso-migration-series"></a>Contoso 迁移系列


我们有一系列文章，展示了虚构的组织 Contoso 如何将其本地基础结构迁移到[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)云。 

该系列包括说明如何设置基础结构迁移以及如何运行不同类型的迁移的方案。 随着方案的进展，其复杂性会增大。 这些文章显示了 Contoso 公司如何处理迁移，但始终提供一般说明和指针。

## <a name="migration-articles"></a>迁移文章

下表总结了此系列中的文章。  

- 每个迁移方案都由略有不同的业务需求驱动，这些业务需求决定了迁移策略。
- 对于每个部署方案，我们提供有关业务驱动因素和目标、建议的体系结构、执行迁移的步骤以及迁移完成后的清理和后续步骤的信息。


**文章** | **详细信息** 
--- | --- 
[第 1 篇：概述](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 概述文章系列、Contoso 的迁移策略和该系列所用的示例应用。 
[文章 2：部署 Azure 基础结构](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso 准备其本地基础结构和 Azure 基础结构进行迁移。 该系列的所有文章共用同一个基础结构。 
[文章 3：评估要迁移到 Azure 的本地资源](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso 评估 VMware 上运行的本地 SmartHotel360 应用。 Contoso 使用 Azure Migrate 服务评估应用 VM，并使用数据迁移助手评估应用 SQL Server 数据库。
[文章 4：在 Azure VM 和 SQL 数据库托管实例上重新托管应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso 将其本地 SmartHotel360 应用直接迁移到 Azure。 Contoso 使用[Azure 迁移](https://docs.microsoft.com/azure/migrate/migrate-services-overview)迁移应用程序前端 VM。 Contoso 使用[Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 Azure SQL 数据库托管实例。
[文章 5：在 Azure VM 上重新托管应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso 使用 Azure 迁移服务将其 SmartHotel360 应用 VM 迁移到 Azure VM。 
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组中重新托管应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso 迁移 SmartHotel360 应用。 Contoso 使用 Azure 迁移迁移应用 VM。 它使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 
[文章 7：将 Linux 应用重新托管到 Azure VM](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso 使用 Azure 迁移完成其 Linux osTicket 应用到 Azure VM 的提升和移动迁移。
[文章 8：在 Azure VM 和 Azure Database for MySQL 上重新托管 Linux 应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso 使用 Azure 迁移将其 Linux osTicket 应用迁移到 Azure VM。 它使用 Azure 数据库迁移服务（包括使用 MySQL 工作台的替代选项）将应用数据库迁移到 MySQ 的 Azure 数据库。
[文章 9：在 Azure Web 应用和 Azure SQL 数据库中重构应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso 将其 SmartHotel360 应用迁移到 Azure Web 应用，并使用 Azure 数据库迁移服务将应用数据库迁移到 Azure SQL Server 实例。
[第 10 条：使用 Azure 应用服务和 SQL 托管实例重构 Windows 应用](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso 将基于 Windows 的本地应用迁移到 Azure Web 应用，并使用 Azure 数据库迁移服务将应用数据库迁移到 Azure SQL 托管实例。
[第 11 条：在 Azure Web 应用和 MySQL 的 Azure 数据库中重构 Linux 应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso 使用与 GitHub 集成的 Azure 流量管理器将其 Linux osTicket 应用迁移到多个 Azure 区域中的 Azure Web 应用，以便持续传递。 Contoso 将应用数据库迁移到 Azure Database for MySQL 实例。 
[第 12 条：在 Azure DevOps 服务上重构团队基础服务器](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso 将其本地 Team Foundation Server 部署迁移到 Azure 中的 Azure DevOps Services。
[文章 13：在 Azure 中重新生成应用](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso 使用一系列 Azure 功能和服务重建其 SmartHotel 应用，包括 Azure 应用服务、Azure 库伯奈斯服务 （AKS）、Azure 函数、Azure 认知服务和 Azure 宇宙数据库。
[文章 14：到 Azure 的大规模迁移](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | 尝试过组合迁移后，Contoso 准备大规模整体迁移到 Azure。



## <a name="next-steps"></a>后续步骤

- [了解](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/)云迁移。
- 在[数据库迁移指南](https://datamigration.microsoft.com/)中了解其他方案（源/目标对）的迁移策略。
