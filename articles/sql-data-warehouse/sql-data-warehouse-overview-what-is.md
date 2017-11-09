---
title: "什么是 Azure SQL 数据仓库？ | Microsoft Docs"
description: "企业级分布式数据库，可处理 PB 量级的关系数据和非关系数据。 它是行业首个云数据仓库，可以在数秒内增长、收缩和暂停。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: d5ad5b566bd8d40ab6d7a9151af54890fd47cc88
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>什么是 Azure SQL 数据仓库？

SQL 数据仓库是基于云的企业数据仓库 (EDW)，可利用大规模并行处理 (MPP) 对数 PB 的数据快速运行复杂的查询。 将 SQL 数据仓库用作大数据解决方案的关键组件。 使用简单的 PolyBase T-SQL 查询将大数据导入 SQL 数据仓库，然后利用 MPP 运行高性能分析。 进行集成和分析时，数据仓库是企业获取见解能够依赖的唯一事实来源。  


## <a name="key-component-of-big-data-solution"></a>大数据解决方案的关键组件
SQL 数据仓库是云中端到端大数据解决方案的关键组件。

![数据仓库解决方案](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

在云数据解决方案中，可从各种源将数据引入大数据存储中。 将数据置于大数据存储中以后，Hadoop、Spark 和机器学习算法就可以准备和训练数据。 当数据准备就绪，可以进行复杂的分析时，SQL 数据仓库就会使用 PolyBase 来查询大数据存储。 PolyBase 使用标准 T-SQL 查询将数据引入 SQL 数据仓库中。
 
SQL 数据仓库通过列存储将数据存储到关系表中。 此格式可显著降低数据存储费用，改进查询性能。 将数据存储到 SQL 数据仓库以后，即可大规模地运行分析。 与传统数据库系统相比，数分钟的分析查询只需数秒即可完成，数天的查询只需数小时。 

分析结果可以传输到世界各地的报告数据库或应用程序。 然后即可通过业务分析获得进行明智的业务决策所需的见解。

## <a name="optimization-choices"></a>优化选项

SQL 数据仓库提供[性能层](performance-tiers.md)，方便用户根据数据需求（不管大小）进行灵活的设置。 可以选择经过弹性优化或计算优化的数据仓库。 

- 弹性优化性能层在体系结构中将计算层和存储层分开。 此选项的优势是工作负荷，后者可以根据短期的高峰活动频繁进行伸缩，从而充分利用独立的计算层和存储层。 此计算层的价格门槛最低，可以根据大多数客户工作负荷的需求进行伸缩。

- 计算优化性能层使用最新的 Azure 硬件，引入了新的 NVMe 固态磁盘缓存，让访问频率最高的数据靠近 CPU，这正是你所需要的。 此性能层可以对存储自动分层，尤其适合复杂的查询，因为所有 IO 对计算层来说都是本地的。 另外，列存储进行了增强，以便在 SQL 数据仓库中存储无限数量的数据。 计算优化性能层提供最高级别的可伸缩性，允许你扩展到 30,000 个计算数据仓库单位 (cDWU)。 对于需要进行持续且速度极快的操作的工作负荷，请选择此层。

## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请继续学习如何快速[创建 SQL 数据仓库][create a SQL Data Warehouse]和[加载示例数据][load sample data]。 如果不熟悉 Azure，在遇到新术语时，可以参考 [Azure 术语表][Azure glossary]。 或者，查看一下以下一些其他 SQL 数据仓库资源。  

* [客户成功案例]
* [博客]
* [功能请求]
* [视频]
* [客户顾问团队博客]
* [创建支持票证]
* [MSDN 论坛]
* [堆栈溢出论坛]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[创建支持票证]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[客户成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客户顾问团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 论坛]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[堆栈溢出论坛]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/en-us/support/legal/sla/
