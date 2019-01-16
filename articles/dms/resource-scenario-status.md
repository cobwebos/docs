---
title: 数据库迁移方案状态 | Microsoft Docs
description: 详细了解 Azure 数据库迁移服务支持的迁移方案的状态。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/08/2019
ms.openlocfilehash: 9e153cca321e94233cfda2a03cf52ba85a0f6b02
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102799"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Azure 数据库迁移服务支持的迁移方案的状态
Azure 数据库迁移服务旨在支持脱机（一次性）和联机（连续同步）迁移的各种迁移方案（源/目标对）。 随着时间的推移，Azure 数据库迁移服务提供的方案范围也在不断扩大。 将定期添加新的方案。 这篇文章介绍了 Azure 数据库迁移服务当前支持的迁移方案和状态（个人[或有限]预览版、公共预览版或正式发布版）或每个方案。

## <a name="offline-versus-online-migrations"></a>脱机和联机迁移
使用 Azure 数据库迁移服务将数据库迁移到 Azure 时，可以执行脱机或联机迁移。 通过“脱机”迁移，应用程序停机时间在迁移开始的同时开始。 对于“联机”迁移，停机时间仅限于迁移完成时切换到新环境所需的时间。 建议对脱机迁移进行测试，以便确定其停机时间是否可以接受；如果不能接受，请执行联机迁移。

## <a name="migration-scenario-status"></a>迁移方案状态
Azure 数据库迁移服务支持的每种迁移方案的状态随时间而变化。 通常，方案首先在“个人预览版”中发布，并且利用该功能要求客户通过 [DMS 预览站点](https://aka.ms/dms-preview)提交提名。 个人预览版完成后，方案状态将更改为“公共预览版”。 所有 Azure 数据库迁移服务用户都可以利用公共预览版中提供的迁移方案。 但是，迁移方案可能并非在所有区域都可用，并且功能可能会在最终发布之前进行其他更改。 迁移方案变为“正式发布版”时（最终的已发布状态），该功能即已完成，并且可供所有 Azure 数据库迁移服务用户使用。 

## <a name="migration-scenario-support"></a>迁移方案支持

下表显示使用 Azure 数据库迁移服务时支持的迁移方案。

> [!NOTE]
> 如果下面列出的支持方案未出现在用户界面中，请联系[数据迁移团队](mailto:datamigrationteam@microsoft.com)获取详细信息。

### <a name="offline-one-time-migration-support"></a>脱机（一次性）迁移支持
下表显示了 Azure 数据库迁移服务对脱机迁移的支持。

| 目标  | 源 | 支持 |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **Azure SQL VM**  | SQL Server  | ✔ |
|   | Oracle  |   |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB for MySQL**  | MySLQ  |  |
|   | RDS MySQL  |  |
| **Azure DB for PostgresSQL**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>联机（连续同步）迁移支持
下表显示了 Azure 数据库迁移服务对联机迁移的支持。

| 目标  | 源 | 支持 |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **Azure SQL VM**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB for MySQL**  | MySLQ  | ✔ |
|   | RDS MySQL  | ✔ |
| **Azure DB for PostgresSQL**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据库迁移服务和区域可用性的概述，请参阅[什么是 Azure 数据库迁移服务](dms-overview.md)一文。 
