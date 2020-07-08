---
title: 将 Azure SQL 托管实例作为数据库工作负荷目标的 SSIS 迁移
description: 将 Azure SQL 托管实例作为数据库工作负荷目标的 SSIS 迁移。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116313"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>将 Azure SQL 托管实例作为数据库工作负荷目标的 SSIS 迁移

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

将数据库工作负荷从 SQL Server 实例迁移到 Azure SQL 托管实例时，应熟悉 [Azure 数据迁移服务](https://docs.microsoft.com/azure/dms/dms-overview) (DMS) 和[使用 DMS 进行 SQL 托管实例迁移的网络拓扑](https://docs.microsoft.com/azure/dms/resource-network-topologies)。

本文重点讨论如何迁移存储在 SSIS 目录 (SSISDB) 中的 SQL Server Integration Service (SSIS) 包以及用于计划 SSIS 包执行的 SQL Server 代理作业。

## <a name="migrate-ssis-catalog-ssisdb"></a>迁移 SSIS 目录 (SSISDB)

SSISDB 迁移可以使用 DMS 来完成，如下文所述：[将 SSIS 包迁移到 SQL 托管实例](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)。

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>将 SSIS 作业迁移到 SQL 托管实例代理

SQL 托管实例有一个一流的本机计划程序，就像本地 SQL Server 代理一样。  由于适合 SSIS 作业的迁移工具尚未发布，因此必须通过脚本/手动复制方式将 SSIS 作业从本地 SQL Server 代理迁移到 SQL 托管实例代理。

## <a name="additional-resources"></a>其他资源

- [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)
- [使用 DMS 进行 SQL 托管实例迁移的网络拓扑](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [将 SSIS 包迁移到 SQL 托管实例](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>后续步骤

- [连接到 Azure 中的 SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [运行部署在 Azure 中的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
