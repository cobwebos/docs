---
title: 将解决方案迁移到 SQL 数据仓库 | Microsoft 文档
description: 有关将解决方案转移到 Azure SQL 数据仓库平台的迁移指南。
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 03/25/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 04c921282d3591e7326d326c230bf72e7f5c1812
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776214"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>将解决方案迁移到 Azure SQL 数据仓库
请参阅将现有数据库解决方案迁移到 Azure SQL 数据仓库所涉及的内容。 

## <a name="profile-your-workload"></a>配置工作负载
在迁移之前，需要确定 SQL 数据仓库是适用于工作负载的理想解决方案。 SQL 数据仓库是分布式系统，旨在对大型数据进行分析。  迁移到 SQL 数据仓库需要一些设计更改，这些更改不难理解但可能需要一些时间才能实现。 如果业务要求企业级的数据仓库，那么花费一些时间来实现这些优点是值得的。 但是，如果不需要改进 SQL 数据仓库，那么使用 SQL Server 或 Azure SQL 数据库则更经济实惠。

在以下情况下，请考虑使用 SQL 数据仓库：
- 具有 1 TB 或数 TB 的数据
- 计划对大量数据运行分析
- 需要能够缩放计算和存储 
- 想要在不需要资源时通过暂停计算来节省成本。

不要将 SQL 数据仓库用于具有以下特点的操作 (OLTP) 工作负载：
- 高频率读取和写入
- 大量的单一实例选择
- 大容量单行插入
- 逐行处理需求
- 不兼容的格式（JSON 和 XML）

## <a name="plan-the-migration"></a>规划迁移

在决定要将现有解决方案迁移到 SQL 数据仓库后，请务必在开始操作之前先规划迁移。 

规划的一个目标是确保数据、表架构以及代码与 SQL 数据仓库兼容。 当前系统和 SQL 数据仓库之间存在一些需要解决的兼容性差异。 此外，将大量数据迁移到 Azure 需要时间。 更具体的规划可以加快将数据迁移到 Azure 的速度。 

规划的另一个目标是进行设计方面的调整，以确保解决方案可以使用 SQL 数据仓库提供的高查询性能。 由于针对缩放性设计数据仓库引入了不同的设计模式，因此传统的方法不一定最合适。 尽管在迁移后可以进行某些设计调整，但是在过程中尽早进行更改可以为以后的操作节省时间。

若要执行成功迁移，需要迁移表架构、代码和数据。 有关以下迁移主题的指南，请参阅：

-  [迁移架构](sql-data-warehouse-migrate-schema.md)
-  [迁移代码](sql-data-warehouse-migrate-code.md)
-  [迁移数据](sql-data-warehouse-migrate-data.md)。 

## <a name="next-steps"></a>后续步骤
CAT（客户顾问团队）也有一些很好的通过博客发布的 SQL 数据仓库指南。  有关迁移的其他指南，请参阅他们的[在实践中将数据迁移到 Azure SQL 数据仓库][Migrating data to Azure SQL Data Warehouse in practice] 一文。

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/

<!--Update_Description: update meta properties, wording update-->