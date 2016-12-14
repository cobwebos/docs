---
title: "Stretch Database 的限制 | Microsoft 文档"
description: "了解 Stretch Database 的限制。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 0a865872-7d42-4873-99b9-cbae45691e54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: anvang
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c1599dfc6aee8adf7ec8714c392a7d8d4427d78


---
# <a name="limitations-for-stretch-database"></a>Stretch Database 的限制
了解已启用延伸的表的限制，以及当前会阻止为表启用延伸的限制。

## <a name="a-namecaveatsa-limitations-for-stretch-enabled-tables"></a><a name="Caveats"></a>已启用延伸的表的限制
已启用延伸的表存在以下限制。

### <a name="constraints"></a>约束
* 没有对包含迁移数据的 Azure 表中的 UNIQUE 约束和 PRIMARY KEY 约束强制实施唯一性。

### <a name="dml-operations"></a>DML 操作
* 在启用了延伸的表或包含启用了延伸的表的视图中不能“更新”或“删除”已迁移的行或可进行迁移的行。
* 无法将行插入链接服务器上的已启用延伸的表。

### <a name="indexes"></a>索引
* 无法为包含已启用延伸的表的视图创建索引。
* 对 SQL Server 索引创建的筛选器不会传播到远程表。

## <a name="a-namelimitationsa-limitations-that-currently-prevent-you-from-enabling-stretch-for-a-table"></a><a name="Limitations"></a>当前会阻止你为表启用延伸的限制
以下各项当前会阻止你为表启用延伸。

### <a name="table-properties"></a>表属性
* 列超过 1,023 个或索引超过 998 个的表
* 包含 FILESTREAM 数据的 FileTables 或表
* 复制的或者正在实际使用更改跟踪或更改数据捕获的表
* 已优化内存的表

### <a name="data-types"></a>数据类型
* text、ntext 和 image
* timestamp
* sql\_variant
* XML
* CLR 数据类型，包括 geometry、geography、hierarchyid 和 CLR 用户定义的类型

### <a name="column-types"></a>列类型
* COLUMN\_SET
* 计算列

### <a name="constraints"></a>约束
* 默认约束和检查约束
* 引用表的外键约束。 在父\-子关系中\(例如，Order 和 Order\_Detail\)，可以为子表 \(Order\_Detail\) 启用延伸，但不能为父表 \(Order\) 启用延伸。

### <a name="indexes"></a>索引
* 全文索引
* XML 索引
* 空间索引
* 引用表的索引视图

## <a name="see-also"></a>另请参阅
[通过运行延伸数据库顾问来识别符合 Stretch Database 条件的数据库和表](sql-server-stretch-database-identify-databases.md)

[为数据库启用 Stretch Database](sql-server-stretch-database-enable-database.md)

[为表启用 Stretch Database](sql-server-stretch-database-enable-table.md)




<!--HONumber=Nov16_HO3-->


