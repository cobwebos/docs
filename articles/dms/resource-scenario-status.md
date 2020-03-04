---
title: 数据库迁移方案状态
titleSuffix: Azure Database Migration Service
description: 了解 Azure 数据库迁移服务支持的迁移方案的状态。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254916"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure 数据库迁移服务支持的迁移方案的状态

Azure 数据库迁移服务旨在支持不同的迁移方案（源/目标对），以便进行脱机（一次性）和联机（连续同步）迁移。 Azure 数据库迁移服务提供的方案覆盖将随着时间的推移而扩展。 将定期添加新的方案。 本文列出了 Azure 数据库迁移服务目前支持的迁移方案，以及每种方案的状态（个人预览版、公共预览版或公开上市）。

## <a name="offline-versus-online-migrations"></a>脱机和联机迁移

使用 Azure 数据库迁移服务，可以执行脱机或联机迁移。 通过“脱机”迁移，应用程序停机时间在迁移开始的同时开始。 若要在迁移完成后将停机时间限制为所需的时间，以减少到新环境，请使用*在线*迁移。 建议测试脱机迁移以确定停机时间是否可接受;否则，请执行联机迁移。

## <a name="migration-scenario-status"></a>迁移方案状态

Azure 数据库迁移服务支持的迁移方案的状态随时间而变化。 通常情况下，在**个人预览版**中首先发布方案。 参与个人预览版要求客户通过[DMS 预览网站](https://aka.ms/dms-preview)提交提名。 在个人预览版后，方案状态将更改为**公共预览版**。 Azure 数据库迁移服务用户可以直接从用户界面尝试公共预览版中的迁移方案。 无需注册。  但是，公共预览版中的迁移方案可能无法在所有区域中使用，并且可能会在最终发布之前进行其他更改。 公开预览版后，方案状态将更改为 "**正式可用性**"。 公开发布（GA）是最终版本状态，并且所有用户均可访问该功能。

## <a name="migration-scenario-support"></a>迁移方案支持

下表显示了使用 Azure 数据库迁移服务时支持的迁移方案。

> [!NOTE]
> 如果在用户界面中列出了如下所示的方案，请联系[请求 Azure 数据库迁移](mailto:AskAzureDatabaseMigrations@service.microsoft.com)别名获取其他信息。

> [!IMPORTANT]
> 若要查看 Azure 数据库迁移服务目前在个人预览版中支持的所有方案，请参阅[DMS 预览站点](https://aka.ms/dms-preview)。

### <a name="offline-one-time-migration-support"></a>脱机（一次性）迁移支持

下表显示了 Azure 数据库迁移服务对脱机迁移的支持。

| 目标  | 源 | 支持 | 状态 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **用于 PostgreSQL 的 Azure DB** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>联机（连续同步）迁移支持

下表显示了 Azure 数据库迁移服务对联机迁移的支持。

| 目标  | 源 | 支持 | 状态 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | 个人预览版 |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **用于 PostgreSQL 的 Azure DB** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 公共预览版 |

## <a name="next-steps"></a>后续步骤

有关 Azure 数据库迁移服务和区域可用性的概述，请参阅[什么是 Azure 数据库迁移服务](dms-overview.md)一文。
