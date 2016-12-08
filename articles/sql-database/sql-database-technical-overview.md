---
title: "什么是 SQL 数据库？ SQL 数据库简介 | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "SQL 简介, 什么是 SQL 数据库"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 1c603d37735bbbfdfaaf4a191e2ad1ce6ff5b2b7
ms.openlocfilehash: 67f3e923680a9a2f399c0839d2ec11ef4615da00


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>什么是 SQL 数据库？ SQL 数据库简介
SQL 数据库是云中的关系数据库服务，它基于行业领先的 Microsoft SQL Server 引擎并提供任务关键型功能。 SQL 数据库提供可预测的性能、在不停机的情况下进行缩放、业务连续性和数据保护以及近乎实时的管理功能。 你可以将注意力集中在如何快速进行应用开发、加快推向市场，而不是管理虚拟机和基础结构上。 由于 SQL 数据库基于 [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) 引擎，所以它支持现有的 SQL Server 工具、库和 API，从而使你可以更轻松地移动和扩展到云中。

本文将介绍 SQL 数据库在性能、缩放性和易管理性方面的核心概念与功能，并提供链接让你进一步了解详细信息。 如果你已准备就绪，可以在几分钟内[创建第一个 SQL 数据库](sql-database-get-started.md)，或者[创建弹性数据库池](sql-database-elastic-pool-create-portal.md)。 如果想深入了解，请观看这个时长 30 分钟的视频。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON326/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>无需停机即可调整性能和规模
SQL 数据库采用基本、标准和高级服务层。 每个服务层提供[不同级别的性能和功能](sql-database-service-tiers.md)，以支持从轻型到重型的数据库工作负荷。 你可以在小型数据库中构建第一个应用，每个月只需花费少量的资金。然后在你的应用受到广泛欢迎之后，随时手动或以编程方式[更改服务层](sql-database-scale-up.md)，这不会给你的应用或客户造成停机。

许多业务和应用只要能够创建数据库并按需调高或调低单一数据库的性能即可，尤其是当使用模式相对容易预测时。 但如果有无法预测的使用模式，则管理成本和业务模式就会变得相当困难。

[弹性池](sql-database-elastic-pool.md) 可解决此问题。 概念很简单。 你可以向池分配性能，并且仅需为池的总体性能付费，而无需为单一数据库的性能付费。 无需调整数据库的性能。 池中的数据库称为 *弹性数据库*，可自动增大和减小来满足需求。 弹性数据库会使用该池，但不会超出其限制，因此即使数据库的使用情况仍不可预测，你的成本也仍是可预测的。 此外，你可以 [向池添加和删除数据库](sql-database-elastic-pool-manage-portal.md)，将应用从少量数据库扩展到数千个，而一切费用不会超出由你控制的预算范围。 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

无论使用单一数据库还是弹性数据库，你都可以随时更改选择。 可将单一数据库与弹性数据库池混合使用，并快速而轻松地更改单一数据库和池的服务层来适应你的情况。 而且，凭借 Azure 的功能和作用范围，你可以将其他 Azure 服务与 SQL 数据库搭配使用以满足独特的应用程序设计需求，提高成本和资源效益，发掘新的商机。

但是，要如何比较数据库和数据库池的相对性能？ 当调高和调低性能时，如何知道该在何处停止？ 答案是依靠内置的性能监视和警报工具，并配合使用基于数据库事务单位 (DTU)（对于单一数据库）和弹性 DTU (eDTU)（对于弹性数据库和数据库池）的性能评级，这使得你可以快速评估根据你的当前需求或项目性能需求向上或向下缩放会带来的影响。 有关详细信息，请参阅 [SQL 数据库选项和性能：了解每个服务层提供的功能](sql-database-service-tiers.md) 。

## <a name="keep-your-app-and-business-running"></a>使应用和业务持续运转
Azure 行业领先的 99.99% 可用性服务级别协议 [(SLA)](http://azure.microsoft.com/support/legal/sla/)（由 Microsoft 管理的数据中心的全球网络提供支持），有助于保持应用全天候运行。 对于每个 SQL 数据库，你都可以使用内置的安全性、容错和数据保护功能，而使用其他产品时可能需要另外购买或设计、构建并管理这些功能。 而且，根据业务需求，你还可以请求额外级别的保护，以确保在发生灾难、错误或其他中断时，应用和业务可以快速恢复。 对于 SQL 数据库，每个服务层都提供了一组全面的业务连续性功能和选项，可以使用这些功能和选项快速进入工作状态并保持运行。 你可以使用时间点还原将数据库还原到以前的状态，最长可还原到 35 天前。 此外，如果托管数据库的数据中心发生服务中断，你可以从最新备份的地域冗余拷贝还原数据库，或者故障转移到其他区域中的数据库副本。 还可以使用副本进行升级，或者转移到不同的区域。

![SQL 数据库异地复制](./media/sql-database-technical-overview/azure_sqldb_map.png)

有关可用于不同服务层的其他业务连续性功能的详细信息，请参阅 [业务连续性](sql-database-business-continuity.md) 。

## <a name="secure-your-data"></a>保护数据
SQL Server 的数据安全性一贯可靠，SQL 数据库也包含类似的功能，可以限制访问、保护数据，并帮助你监视活动。 请参阅 [保护你的 SQL 数据库](sql-database-security.md) ，大致了解你在 SQL 数据库中拥有的安全选项。 有关更全面的安全功能视图，请参阅 [SQL Server 数据库引擎和 SQL 数据库安全中心](https://msdn.microsoft.com/library/bb510589) 。 有关 Azure 平台安全性的信息，请访问 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/) 。

## <a name="next-steps"></a>后续步骤
现在，你已阅读 SQL 数据库简介并回答了“什么是 SQL 数据库？”这个问题，因此可以继续完成以下内容：

* 有关单一数据库和弹性数据库的成本比较与计算器，请参阅 [定价页](https://azure.microsoft.com/pricing/details/sql-database/) 。
* 了解 [弹性池](sql-database-elastic-pool.md)。
* 从 [创建第一个数据库](sql-database-get-started.md)开始。
* [使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
* 使用 C#、Java、Node.js、PHP、Python 或 Ruby 生成第一个应用： [SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)



<!--HONumber=Nov16_HO5-->


