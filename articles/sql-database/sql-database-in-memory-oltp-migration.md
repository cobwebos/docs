---
title: "内存中 OLTP 改善了 SQL 事务性能 | Microsoft 文档"
description: "利用内存中 OLTP 改善现有 SQL 数据库中的事务性能。"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: MightyPen
ms.assetid: c2bf14a0-905b-47b4-afb6-efe9a61147d5
ms.service: sql-database
ms.custom: develop databases
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jodebrui
ms.openlocfilehash: 71dd7d36eee210b80ed6a791b52f977a416b6bb7
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>使用内存中 OLTP 改善 SQL 数据库中的应用程序性能
[内存中 OLTP](sql-database-in-memory.md) 可以用来改善[高级](sql-database-service-tiers.md) Azure SQL 数据库中的事务处理、数据引入的性能以及暂时性数据状况，且不需要提高定价层。 

> [!NOTE] 
> 了解 [Quorum 如何使用 SQL 数据库将关键数据库的工作负荷提高一倍，并将 DTU 降低 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


请按照以下步骤在现有数据库中采用内存中 OLTP。

## <a name="step-1-ensure-you-are-using-a-premium-database"></a>步骤 1：确保使用的是高级数据库
只有高级数据库才支持内存中 OLTP。 如果返回的结果为 1（不是 0），则支持 In-Memory：

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* 代表*极端事务处理*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>步骤 2：标识要迁移到 In-Memory OLTP 的对象
SSMS 包含可以针对具有活动工作负荷的数据库运行的**事务性能分析概述**。 该报告识别要迁移到 In-Memory OLTP 的候选表和存储过程。

若要在 SSMS 中生成报告，请执行以下操作：

* 在“对象资源管理器”中，右键单击数据库节点。
* 单击“报表” > “标准报表” > “事务能分析概述”。

有关详细信息，请参阅[确定表或存储过程是否应移植到内存中 OLTP](http://msdn.microsoft.com/library/dn205133.aspx)

## <a name="step-3-create-a-comparable-test-database"></a>步骤 3：创建可比较的测试数据库
假设报告指出数据库的某个表在转换成内存优化的表后会带来好处。 我们建议先进行测试，以确认这项指示。

需要创建生产数据库的测试副本。 测试数据库应当位于与生产数据库相同的服务层级别。

为了简化测试，请按以下方式调整测试数据库：

1. 使用 SSMS 连接到测试数据库。
2. 若要避免在查询中用到 WITH (SNAPSHOT) 选项，请按照以下 T-SQL 语句中所示设置数据库选项：
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>步骤 4：迁移表
必须创建并填充想要测试的表的内存优化副本。 可以使用以下方式之一来创建该副本：

* SSMS 中提供便利的内存优化向导。
* 手动 T-SQL。

#### <a name="memory-optimization-wizard-in-ssms"></a>SSMS 中提供的内存优化向导
若要使用此迁移选项，请执行以下操作：

1. 使用 SSMS 连接到测试数据库。
2. 在“对象资源管理器”中，右键单击该表，并单击“内存优化顾问”。
   
   * 此时会显示“表内存优化顾问”向导。
3. 在向导中，单击“迁移验证”（或“下一步”按钮），以查看该表是否有任何在内存优化表中不受支持的功能。 有关详细信息，请参阅：
   
   * [内存优化顾问中的](http://msdn.microsoft.com/library/dn284308.aspx)*内存优化清单*。
   * [内存中 OLTP 不支持的 Transact-SQL 构造](http://msdn.microsoft.com/library/dn246937.aspx)。
   * [迁移到内存中 OLTP](http://msdn.microsoft.com/library/dn247639.aspx)。
4. 如果该表没有不受支持的功能，顾问可执行实际的架构和数据迁移。

#### <a name="manual-t-sql"></a>手动 T-SQL
若要使用此迁移选项，请执行以下操作：

1. 使用 SSMS（或类似的实用程序）连接到测试数据库。
2. 获取表及其索引的完整 T-SQL 脚本。
   
   * 在 SSMS 中，右键单击表节点。
   * 单击“编写表脚本为” > “创建到” > “新建查询窗口”。
3. 在脚本窗口中，将 WITH (MEMORY_OPTIMIZED = ON) 添加到 CREATE TABLE 语句。
4. 如果存在 CLUSTERED 索引，请将其更改为 NONCLUSTERED。
5. 使用 SP_RENAME 重命名现有表。
6. 通过运行已编辑的 CREATE TABLE 脚本，创建新的内存优化表副本。
7. 使用 INSERT...SELECT * INTO 将数据复制到内存优化表：

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>步骤 5（可选）：迁移存储过程
In-Memory 功能还可以修改存储过程，以改善性能。

### <a name="considerations-with-natively-compiled-stored-procedures"></a>本机编译存储过程的注意事项
本机编译存储过程的 T-SQL WITH 子句必须包含以下选项：

* NATIVE_COMPILATION
* SCHEMABINDING：表示除非丢弃存储过程，否则无法由存储过程以任何影响到存储过程的方式更改其列定义的表。

本机模块必须使用一个大型 [ATOMIC 块](http://msdn.microsoft.com/library/dn452281.aspx)进行事务管理。 显式 BEGIN TRANSACTION 或 ROLLBACK TRANSACTION 没有角色。 如果代码检测到违反业务规则，它可以使用 [THROW](http://msdn.microsoft.com/library/ee677615.aspx) 语句终止原子块。

### <a name="typical-create-procedure-for-natively-compiled"></a>本机编译存储过程的典型 CREATE PROCEDURE
创建本机编译存储过程的 T-SQL 通常类似于以下模板：

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* 就 TRANSACTION_ISOLATION_LEVEL 而言，SNAPSHOT 是本机编译存储过程最常用的值。 但是，也支持其他值的子集：
  
  * REPEATABLE READ
  * SERIALIZABLE
* sys.languages 视图中必须存在 LANGUAGE 值。

### <a name="how-to-migrate-a-stored-procedure"></a>如何迁移存储过程
迁移步骤如下：

1. 获取常规解释的存储过程的 CREATE PROCEDURE 脚本。
2. 重写其标头以符合前面的模板。
3. 确认存储过程 T-SQL 代码是否使用了任何不支持本机编译存储过程的功能。 根据需要实施应对措施。
   
   * 有关详细信息，请参阅[本机编译存储过程的迁移问题](http://msdn.microsoft.com/library/dn296678.aspx)。
4. 使用 SP_RENAME 重命名旧存储过程。 或直接将它删除。
5. 运行已编辑的 CREATE PROCEDURE T-SQL 脚本。

## <a name="step-6-run-your-workload-in-test"></a>步骤 6：在测试环境中运行工作负荷
在测试数据库中，运行与在生产数据库中运行的工作负荷类似的工作负荷。 这样，将 In-Memory 功能用于表和存储过程所达到的性能改善应可体现出来。

工作负荷的主要属性包括：

* 并发连接数。
* 读/写比率。

若要修改并运行测试工作负荷，请考虑使用[此处](sql-database-in-memory.md)所示的 ostress.exe 便利工具。

为了尽可能减少网络延迟，请在数据库所在的同一 Azure 地理区域运行测试。

## <a name="step-7-post-implementation-monitoring"></a>步骤 7：实施后的监视
建议监视在生产环境中实施 In-Memory 后的性能影响：

* [监视内存中存储](sql-database-in-memory-oltp-monitoring.md)。
* [使用动态管理视图监视 Azure SQL 数据库](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>相关链接
* [内存中 OLTP（内存中优化）](http://msdn.microsoft.com/library/dn133186.aspx)
* [本机编译的存储过程简介](http://msdn.microsoft.com/library/dn133184.aspx)
* [内存优化顾问](http://msdn.microsoft.com/library/dn284308.aspx)

