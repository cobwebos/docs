---
title: "将架构迁移到 SQL 数据仓库 | Microsoft 文档"
description: "有关在开发解决方案时将架构迁移到 Azure SQL 数据仓库的技巧。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1ac4db6d9404567be0d3d12f91a96fa4e31ef3bf


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>将架构迁移到 SQL 数据仓库
以下摘要有助于理解 SQL Server 与 SQL 数据仓库之间的差异，方便用户迁移数据库。

## <a name="table-migration"></a>表迁移
在迁移表时，需熟悉 SQL 数据仓库表的表功能。  一开始可参阅[表概述][表概述]。  本文介绍了在创建表（例如表统计信息、分布、分区和索引）时需要考虑的最重要事项。  同时还介绍了一些[不支持的表功能][不支持的表功能]和解决方法。

SQL 数据仓库支持常见的业务数据类型。  请参阅[数据类型][数据类型]一文，了解一系列受支持的和[不支持的数据类型][不支持的数据类型]。  [数据类型][数据类型]一文还包含一个查询，用于标识[不支持的数据类型][不支持的数据类型]。  转换数据类型时，请确保参阅[数据类型最佳实践][数据类型最佳实践]。

## <a name="next-steps"></a>后续步骤
成功将数据库架构迁移到 SQL 数据仓库后，请继续阅读下列文章之一：

* [迁移数据][迁移数据]
* [迁移代码][迁移代码]

有关 SQL 数据仓库最佳实践的详细信息，请参阅[最佳实践][最佳实践]一文。

<!--Image references-->

<!--Article references-->
[迁移代码]: ./sql-data-warehouse-migrate-code.md
[迁移数据]: ./sql-data-warehouse-migrate-data.md
[最佳实践]: ./sql-data-warehouse-best-practices.md
[表概述]: ./sql-data-warehouse-tables-overview.md
[不支持的表功能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[数据类型]: ./sql-data-warehouse-tables-data-types.md
[不支持的数据类型]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[数据类型最佳实践]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


