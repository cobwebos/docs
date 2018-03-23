---
title: 欢迎使用 Wingtips 应用 - Azure SQL 数据库 | Microsoft Docs
description: 了解有关云环境中的 Azure SQL 数据库的数据库租户模型和示例 Wingtips SaaS 应用程序的内容。
keywords: sql 数据库教程
services: sql-database
author: billgib
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: bb69a03333ed9dcdba1456d053c0080be5ba4d10
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS 应用程序

三个示例均实现了同一个 Wingtip Tickets SaaS 应用程序。 此应用是面向小型场所（电影院、俱乐部等）的事件清单和票证 SaaS 应用。每个场所都是此应用的一个租户，并有自己的数据：场所详细信息、事件列表、客户、票证订单等。此应用与管理脚本和教程一起展示了一个端到端的 SaaS 方案。 此方案包括预配租户、监视并管理性能、架构管理和跨租户报告和分析。

## <a name="three-saas-application-and-tenancy-patterns"></a>三种 SaaS 应用程序和租户模式

此应用有三个版本；每个版本在 Azure SQL 数据库上都使用了不同的数据库租户模式。  第一个示例使用每租户的独立应用程序和其自己的数据库。 第二个示例使用多租户应用，并且每个租户都具有一个数据库。 第三个示例使用多租户应用，并且具有分片式多租户数据库。

![三种租户模式][image-three-tenancy-patterns]

 每个示例包含应用程序代码以及管理脚本和教程，其中介绍了各种设计和管理模式。  每个示例均可在 5 分钟内完成部署。  可并行部署这三个示例，以比较它们在设计和管理方面的不同。

## <a name="standalone-application-per-tenant-pattern"></a>“每租户一个独立应用程序”模式

“每租户一个独立应用程序”模式使用单个租户应用程序，并且每个租户具有一个数据库。 每个租户的应用，包括其数据库，均部署到单独的 Azure 资源组。 可在服务提供商的订阅或租户的订阅中部署资源组，并由提供程序代表租户进行管理。 每租户独立应用程序模式提供了最佳的租户隔离，但是它的经济成本通常是最高的，因为无法跨多个租户共享资源。  此模式非常适合于较为复杂并部署到较小数量的租户的应用程序。  相较其他模式，使用独立部署，能够更轻松地为每个租户自定义应用程序。  

请查看相关[教程][docs-tutorials-for-wingtip-sa]和 GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa] 上的代码。

## <a name="database-per-tenant-pattern"></a>“每个租户一个数据库”模式

“每个租户一个数据库”模式适用于注重租户隔离并想要运行集中式服务以经济高效地使用共享资源的服务提供商。 将为每个场所或租户创建数据库，并集中管理所有数据库。 数据库可以托管在弹性池中，以便可以均衡无法预测的租户工作负载模式，轻松并经济高效地进行性能管理。 目录数据库将保管租户与其数据库之间的映射。 使用[弹性数据库客户端库](sql-database-elastic-database-client-library.md)的分片映射管理功能管理此映射，从而为应用程序提供高效的连接管理功能。

请查看相关[教程][docs-tutorials-for-wingtip-dpt]和 GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt] 上的代码。

## <a name="sharded-multi-tenant-database-pattern"></a>分片式多租户数据库模式

多租户数据库适用于希望降低每个租户的成本并可接受租户隔离性降低的服务提供商。 此模式可将大量租户封装到单个数据库，从而降低每个租户的成本。 可以通过跨多个数据库将租户分片来实现几乎无限制的缩放。 目录数据库将租户映射到数据库。  

此模式还可实现混合模型，在此模型中可以通过将多个租户置于一个数据库中来优化成本，或通过将单个租户置于他们自己的数据库中来优化隔离。 在预配租户时或在此之后，可以根据租户做出选择，并且不会影响此应用程序。  如果需要以不同的方式处理不同租户组，使用此模式可获得良好效果。 例如，可将低成本租户分配到共享数据库，而将高级租户分配到其自己的数据库。 

请查看相关[教程][docs-tutorials-for-wingtip-mt]和 GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt] 上的代码。

## <a name="next-steps"></a>后续步骤

#### <a name="conceptual-descriptions"></a>概念说明

- 可在[多租户 SaaS 数据库租户模式][saas-tenancy-app-design-patterns-md]中查看此应用程序租户模式的详细说明

#### <a name="tutorials-and-code"></a>教程和代码

- 每租户独立应用程序：
    - [独立应用教程][docs-tutorials-for-wingtip-sa]。
    - [GitHub 上独立应用的代码][github-code-for-wingtip-sa]。

- 每个租户一个数据库：
    - [“每个租户一个数据库”模式的教程][docs-tutorials-for-wingtip-dpt]。
    - [GitHub 上的“每个租户一个数据库”模式的代码][github-code-for-wingtip-dpt]。

- 分片式多租户：
    - [分片式多租户模式的教程][docs-tutorials-for-wingtip-mt]。
    - [GitHub 上的分片式多租户模式的代码][github-code-for-wingtip-mt]。



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "三种租户模式。"

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

