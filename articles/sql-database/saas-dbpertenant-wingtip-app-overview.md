---
title: Azure SQL 数据库多租户应用示例 - Wingtip SaaS | Microsoft Docs
description: 借助使用 Azure SQL 数据库的示例多租户应用程序，了解 Wingtip SaaS 示例
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 451e1fc87fc5f626e78760d8cd5c4115ea02bb0d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>多租户 SaaS 应用简介，该应用通过“每租户一个数据库”模式使用 SQL 数据库

Wingtip SaaS 应用程序是多租户应用的一个例子。 该应用使用“每租户一个数据库”（一种 SaaS 应用程序模式）为多个租户提供服务。 该应用使用多个 SaaS 设计及管理模式，展示支持 SaaS 方案的 Azure SQL 数据库功能。 Wingtip SaaS 应用的部署时间不到五分钟，可快速启动并运行！

[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 存储库提供了应用程序源代码和管理脚本。 开始操作前，请先参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)，获取下载和取消阻止 Wingtip Tickets 管理脚本的步骤。

## <a name="application-architecture"></a>应用程序体系结构

Wingtip SaaS 应用使用租户各有数据库模型，并使用 SQL 弹性池来最大程度地提高效率。 为了预配租户并将其映射到租户数据，需使用目录数据库。 核心 Wingtip SaaS 应用程序使用一个池和三个示例租户，外加目录数据库。 完成许多 Wingtip SaaS 教程后，初始部署中会存在附加设备，这是因为引入了分析数据库，跨数据库架构管理等。


![Wingtip SaaS 体系结构](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


学习教程及使用应用时，应关注 SaaS 模式，因为其与数据层相关。 换句话说，请专注于数据层，不要将过多精力放在分析应用本身上。 了解如何实现这些 SaaS 模式对于在应用程序中实现这些模式很重要，同时可考虑按特定的业务要求进行任何必需的修改。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL 数据库 Wingtip SaaS 教程

部署该应用后，请浏览基于初始部署的以下教程。 这些教程探索常见的 SaaS 模式，这些模式利用 SQL 数据库、SQL 数据仓库和其他 Azure 服务的内置功能。 教程包括 PowerShell 脚本，附带十分有助于理解的详细介绍，并在应用程序中实施相同的 SaaS 管理模式。


| 教程 | 说明 |
|:--|:--|
| [Azure SQL 数据库多租户 SaaS 应用示例指南和提示](saas-tenancy-wingtip-app-guidance-tips.md) | 从此处开始！ 下载并运行 PowerShell 脚本，准备应用程序部件。 |
|[部署和浏览 Wingtip SaaS 应用程序](saas-dbpertenant-get-started-deploy.md)|  向 Azure 订阅部署并浏览 Wingtip SaaS 应用程序。 |
|[预配和编录租户](saas-dbpertenant-provision-and-catalog.md)| 了解应用程序如何使用目录数据库连接到租户，以及目录如何将租户映射到其数据。 |
|[监视和管理性能](saas-dbpertenant-performance-monitoring.md)| 了解如何使用 SQL 数据库的监视功能，以及如何在超出性能阈值时设置警报。 |
|[使用 Log Anaytics 进行监视 (OMS)](saas-dbpertenant-log-analytics.md) | 了解如何使用 [Log Analytics](../log-analytics/log-analytics-overview.md) 跨多个池监视大量资源。 |
|[还原单个租户](saas-dbpertenant-restore-single-tenant.md)| 了解如何将租户数据库还原到先前的时间点。 还包括在使现有租户数据库保持联机的情况下，还原为并行数据库的步骤。 |
|[管理租户数据库架构](saas-tenancy-schema-management.md)| 了解如何跨所有租户数据库更新架构和更新引用数据。 |
|[运行跨租户分布式查询](saas-tenancy-cross-tenant-reporting.md) | 创建即席分析数据库并对所有租户运行实时分布式查询。  |
|[对提取的租户数据运行分析](saas-tenancy-tenant-analytics.md) | 将租户数据提取到分析数据库或数据仓库，以运行脱机分析查询。 |


## <a name="next-steps"></a>后续步骤

- [部署和使用 Wingtip Tickets SaaS 应用示例的常规指南和提示](saas-tenancy-wingtip-app-guidance-tips.md)

- [部署 Wingtip SaaS 应用程序](saas-dbpertenant-get-started-deploy.md)
