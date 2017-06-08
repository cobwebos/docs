---
title: "将架构迁移到 SQL 数据仓库 | Microsoft 文档"
description: "有关在开发解决方案时将架构迁移到 Azure SQL 数据仓库的技巧。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0630f43642a0be98c470032d32b74ca14ee144e5
ms.contentlocale: zh-cn
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>将架构迁移到 SQL 数据仓库
以下摘要有助于理解 SQL Server 与 SQL 数据仓库之间的差异，方便用户迁移数据库。

## <a name="table-migration"></a>表迁移
在迁移表时，需熟悉 SQL 数据仓库表的表功能。  建议首先参阅[表概述][table overview]。  本文介绍了在创建表（例如表统计信息、分布、分区和索引）时需要考虑的最重要事项。  同时还介绍了一些[不支持的表功能][unsupported table features]和解决方法。

SQL 数据仓库支持常见的业务数据类型。  请参阅[数据类型][data types]一文，了解受支持和[不支持的数据类型][unsupported data types]的列表。  [数据类型][data types]一文还包含一个用于识别[不支持的数据类型][unsupported data types]的查询。  转换数据类型时，请确保参阅[数据类型最佳实践][data type best practices]。

## <a name="next-steps"></a>后续步骤
成功将数据库架构迁移到 SQL 数据仓库后，请继续阅读下列文章之一：

* [迁移数据][Migrate your data]
* [迁移代码][Migrate your code]

有关 SQL 数据仓库最佳实践的详细信息，请参阅[最佳实践][best practices]一文。

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[data type best practices]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

