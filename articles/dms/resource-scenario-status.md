---
title: 数据库迁移方案状态 | Microsoft Docs
description: 了解有关 Azure 数据库迁移服务支持的迁移方案的状态信息。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/04/2019
ms.openlocfilehash: f25bc9bc3a958b2fa97ae4d5ab3715b602110393
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915469"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>迁移方案支持的 Azure 数据库迁移服务的状态
Azure 数据库迁移服务旨在支持不同迁移方案 （源/目标对） 同时脱机 （一次性） 和联机 （连续同步） 迁移。 随着时间的推移进行了扩展提供的 Azure 数据库迁移服务的方案覆盖范围。 将定期添加新的方案。 此项目用于标识当前支持的 Azure 数据库迁移服务和每个方案的状态 （个人预览版中，公共预览版中或公开上市） 的迁移方案。

## <a name="offline-versus-online-migrations"></a>脱机和联机迁移
使用 Azure 数据库迁移服务，您可以执行脱机或联机迁移。 通过“脱机”迁移，应用程序停机时间在迁移开始的同时开始。 若要限制到所需切换到新环境中完成迁移后的时间的停机时间，请使用*online*迁移。 我们建议将测试以确定是否可接受; 停机时间的脱机迁移否则，请执行联机迁移。

## <a name="migration-scenario-status"></a>迁移方案状态
随着时间而变化的迁移方案支持的 Azure 数据库迁移服务的状态。 通常情况下，在首次发布方案**个人预览版**。 参与个人预览版要求客户提交通过提名[DMS 预览站点](https://aka.ms/dms-preview)。 在个人预览版后方案状态将变为**公共预览版**。 Azure 数据库迁移服务用户可以尝试直接从用户界面的公共预览版中的迁移方案。 不注册是必需的。  但是，在公共预览版中的迁移方案在所有区域中可能不可用，并且还可能进行其他更改之前最终版本。 公开预览版后，方案状态将变为**正式版**。 公开上市 (GA）) 是最终版本状态和功能完成并可供所有用户访问。

## <a name="migration-scenario-support"></a>迁移方案支持
下表显示了使用 Azure 数据库迁移服务时，受支持的迁移方案。

> [!NOTE]
> 如果下面列出的支持方案未出现在用户界面中，请联系[数据迁移团队](mailto:datamigrationteam@microsoft.com)获取详细信息。

> [!IMPORTANT]
> 若要查看当前支持的 Azure 数据库迁移服务个人预览版中的所有方案，请参阅[DMS 预览站点](https://aka.ms/dms-preview)。

### <a name="offline-one-time-migration-support"></a>脱机（一次性）迁移支持
下表显示了 Azure 数据库迁移服务对脱机迁移的支持。

| 目标  | 源 | 支持 | 状态 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL 数据库** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | 公共预览版 |
| **用于 MySQL 的 Azure DB** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **用于 PostgreSQL 的 Azure DB** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>联机（连续同步）迁移支持
下表显示了 Azure 数据库迁移服务对联机迁移的支持。

| 目标  | 源 | 支持 | 状态 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL 数据库** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | 个人预览版 |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | 公共预览版 |
| **用于 MySQL 的 Azure DB** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **用于 PostgreSQL 的 Azure DB** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 个人预览版 |

## <a name="next-steps"></a>后续步骤
Azure 数据库迁移服务和区域可用性的概述，请参阅文章[什么是 Azure 数据库迁移服务](dms-overview.md)。
