---
title: "欢迎使用 Wingtips 应用 - Azure SQL 数据库 | Microsoft Docs"
description: "了解有关云环境中的 Azure SQL 数据库的数据库租户模型和示例 Wingtips SaaS 应用程序的内容。"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>欢迎使用 Wingtip Tickets 示例 SaaS Azure SQL 数据库租户应用

欢迎使用 Wingtip Tickets 示例 SaaS Azure SQL 数据库租户应用程序及其教程。 数据库租户是指应用向为在应用程序中托管而付费的客户端提供的数据隔离模式。 目前，要进一步简化，每个客户端需拥有其自己的完整数据库，或与其他客户端共享数据库。

## <a name="wingtip-tickets-app"></a>Wingtip Tickets 应用

Wingtip Tickets 示例应用程序说明了不同数据库租户模型对多租户 SaaS 应用程序设计和管理的影响。 随附的教程直接介绍了这些相同的影响。 Wingtip Tickets 在 Azure SQL 数据库的基础上构建。

Wingtip Tickets 旨在处理实际 SaaS 客户端使用的各种设计和管理方案。 Wingtip Tickets 中考虑了出现的使用模式。

在五分钟内即可在自己的 Azure 订阅中安装 Wingtip Tickets 应用。 安装包括插入多个租户的示例数据。 可以安全安装应用程序和所有模型的管理脚本，因为安装不会相互影响或相互干扰。

#### <a name="code-in-github"></a>Github 中的代码

应用程序代码和管理脚本均可从 GitHub 上获取：

- “独立应用”模型：[WingtipTicketsSaaS-StandaloneApp 存储库](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
- “每个租户一个数据库”模型：[WingtipTicketsSaaS-DbPerTenant 存储库](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)。
- “分片多租户”模型：[WingtipTicketsSaaS-MultiTenantDB 存储库](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB)。

对上面列出的所有模型重复使用 Wingtip Tickets 应用的同一代码基。 可以使用 Github 中的代码开始自己的 SaaS 项目。



## <a name="major-database-tenancy-models"></a>主要的数据库租户模型

Wingtip Tickets 是一个事件列表和票证 SaaS 应用程序。 Wingtip 按地点提供所需服务。 对每个地点应用以下各项：

- 为在应用程序中托管付费。
- 是 Wingtip 中的“租户”。
- 主机事件。 涉及以下事件：
    - 票证价格。
    - 票证销售数。
    - 购买票证的客户。

应用与管理脚本和教程一起展示完整的 SaaS 方案。 该方案包括以下活动：

- 租户预配。
- 监视和管理性能。
- 架构管理。
- 跨租户报告和分析。

所有这些活动都可根据需要以任意规模提供。



## <a name="code-samples-for-each-tenancy-model"></a>每个租户模型的代码示例

强调了一组应用程序模型。 但是，其他实现可以混合两个或更多模型的元素。

#### <a name="standalone-app-model"></a>“独立应用”模型

![“独立应用”模型][standalone-app-model-62s]

此模型使用单租户应用程序。 因此，此模型只需一个数据库，并且仅为一个租户存储数据。 该租户与数据库中的其他租户完全隔离。

将应用的实例出售给多个不同客户端时，为了使每个客户端独立运行，可以使用此模型。 客户端是唯一的租户。 数据库仅为一个客户端存储数据，但为客户端的多位客户存储数据。

#### <a name="database-per-tenant"></a>每个租户一个数据库

![“每个租户一个数据库”模型][database-per-tenant-model-35d]

此模型时的应用程序实例中有多个租户。 但对于新租户，会分配另一个仅供该新租户使用的数据库。

此模型为每个租户提供完全数据库隔离。 Azure SQL 数据库服务非常精妙，使此模型变得合理。

- [SQL 数据库多租户 SaaS 应用示例简介][saas-dbpertenant-wingtip-app-overview-15d] - 提供有关此模型的详细信息。

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>分片多租户数据库，混合型

![“分片多租户数据库”模型，混合型][sharded-multitenantdb-model-hybrid-79m]

此模型时的应用程序实例中有多个租户。 此外，此模型在其一些或所有数据库中也有多个租户。 此模型有利于提供不同的服务层，以便客户端通过支付更多费用实现完全数据库隔离。

每个数据库的架构都包括租户标识符。 甚至在仅存储一个租户的数据库中都有租户标识符。

- [SQL 数据库多租户 SaaS 应用示例简介][saas-multitenantdb-get-started-deploy-89i]



## <a name="tutorials-for-each-tenancy-model"></a>每个租户模型的教程

以下各项记录了每个租户模型：

- 一组教程文章。
- Github 存储库中存储的模型专用源代码：
    - Wingtip Tickets 应用程序的代码。
    - 管理方案的脚本代码。

#### <a name="tutorials-for-management-scenarios"></a>管理方案教程

每个模型的教程文章涵盖以下管理方案：

- 租户预配。
- 性能监视和管理。
- 架构管理。
- 跨租户报告和分析。
- 将租户还原到以前的某个时间点。
- 灾难恢复。



## <a name="next-steps"></a>后续步骤

- [SQL 数据库多租户 SaaS 应用示例简介][saas-dbpertenant-wingtip-app-overview-15d] - 提供有关此模型的详细信息。

- [多租户 SaaS 数据库租户模式][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "“独立应用”模型"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "“每个租户一个数据库”模型"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "“分片多租户数据库”模型，混合型"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


