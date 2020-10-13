---
title: 内存中 OLTP 改进了 SQL 事务性能
description: 利用内存中 OLTP 改进 Azure SQL 数据库中现有数据库和 Azure SQL 托管实例的事务性能。
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619740"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>使用内存中 OLTP 改进 Azure SQL 数据库和 Azure SQL 托管实例中的应用程序性能
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[内存中 OLTP](in-memory-oltp-overview.md) 可以用来改善[高级和业务关键层](database/service-tiers-vcore.md)数据库中事务处理、数据引入和暂时性数据方案的性能，而不需要提高定价层。

> [!NOTE]
> 了解 [Quorum 如何通过使用 Azure SQL 数据库，在关键数据库工作负载加倍的情况下，将 DTU 降低 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

请按照以下步骤在现有数据库中采用内存中 OLTP。

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>步骤 1：确保使用的是高级和业务关键层数据库

只有高级和业务关键层数据库才支持内存中 OLTP。 如果返回的结果为 1（不是 0），则支持内存中 OLTP：

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

XTP 代表*极端事务处理*

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>步骤 2：标识要迁移到 In-Memory OLTP 的对象

SSMS 包含可以针对具有活动工作负荷的数据库运行的“事务性能分析概述”。 该报告识别要迁移到内存中 OLTP 的候选表和存储过程。

若要在 SSMS 中生成报告，请执行以下操作：

* 在“对象资源管理器”中，右键单击数据库节点。
* 单击“报表” > “标准报表” > “事务性能分析概述”。

有关详细信息，请参阅[确定是否应将某个表或存储过程移植到 In-Memory OLTP](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp)。

## <a name="step-3-create-a-comparable-test-database"></a>步骤 3：创建可比较的测试数据库

假设报告指出数据库的某个表在转换成内存优化的表后会带来好处。 我们建议先进行测试，以确认这项指示。

需要创建生产数据库的测试副本。 测试数据库应当位于与生产数据库相同的服务层级级别。

为了简化测试，请按以下方式调整测试数据库：

1. 使用 SSMS 连接到测试数据库。
2. 若要避免在查询中用到 WITH (SNAPSHOT) 选项，请按照以下 T-SQL 语句中所示设置数据库选项：

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>步骤 4：迁移表

必须创建并填充想要测试的表的内存优化副本。 可以使用以下方式之一来创建该副本：

* SSMS 中提供便利的内存优化向导。
* 手动 T-SQL。

### <a name="memory-optimization-wizard-in-ssms"></a>SSMS 中提供的内存优化向导

若要使用此迁移选项，请执行以下操作：

1. 使用 SSMS 连接到测试数据库。
2. 在“对象资源管理器”中，右键单击该表，然后单击“内存优化顾问”。

   此时将显示“表内存优化顾问”向导。
3. 在向导中，单击“迁移验证”（或“下一步”按钮），查看该表是否包含任何在内存优化表中不受支持的功能。 有关详细信息，请参阅：

   * [内存优化顾问中的](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)内存优化清单。
   * [内存中 OLTP 不支持的 Transact-SQL 构造](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp)。
   * [迁移到内存中 OLTP](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server)。
4. 如果该表没有不受支持的功能，顾问可执行实际的架构和数据迁移。

### <a name="manual-t-sql"></a>手动 T-SQL

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

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>步骤 5（可选）：迁移存储过程

In-Memory 功能还可以修改存储过程，以改善性能。

### <a name="considerations-with-natively-compiled-stored-procedures"></a>本机编译存储过程的注意事项

本机编译存储过程的 T-SQL WITH 子句必须包含以下选项：

* NATIVE_COMPILATION
* SCHEMABINDING：表示除非丢弃存储过程，否则无法由存储过程以任何影响到存储过程的方式更改其列定义的表。

本机模块必须使用一个大型 [ATOMIC 块](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures)进行事务管理。 显式 BEGIN TRANSACTION 或 ROLLBACK TRANSACTION 没有角色。 如果你的代码检测到违反业务规则，它可以使用 [THROW](/sql/t-sql/language-elements/throw-transact-sql) 语句终止 ATOMIC 块。

### <a name="typical-create-procedure-for-natively-compiled"></a>本机编译存储过程的典型 CREATE PROCEDURE

创建本机编译存储过程的 T-SQL 通常类似于以下模板：

```sql
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

* 对于 TRANSACTION_ISOLATION_LEVEL，SNAPSHOT 是本机编译存储过程最常用的值。 但是，也支持其他值的子集：
  
  * REPEATABLE READ
  * SERIALIZABLE
* sys.languages 视图中必须存在 LANGUAGE 值。

### <a name="how-to-migrate-a-stored-procedure"></a>如何迁移存储过程

迁移步骤如下：

1. 获取常规解释的存储过程的 CREATE PROCEDURE 脚本。
2. 重写其标头以符合前面的模板。
3. 确认存储过程 T-SQL 代码是否使用了任何不支持本机编译存储过程的功能。 根据需要实施应对措施。

   有关详细信息，请参阅[本机编译存储过程的迁移问题](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)。
4. 使用 SP_RENAME 重命名旧存储过程。 或直接将它删除。
5. 运行已编辑的 CREATE PROCEDURE T-SQL 脚本。

## <a name="step-6-run-your-workload-in-test"></a>步骤 6：在测试环境中运行工作负荷

在测试数据库中，运行与在生产数据库中运行的工作负荷类似的工作负荷。 这样，将 In-Memory 功能用于表和存储过程所达到的性能改善应可体现出来。

工作负荷的主要属性包括：

* 并发连接数。
* 读/写比率。

若要修改并运行测试工作负载，请考虑使用便利的 `ostress.exe` 工具，这篇[内存中](in-memory-oltp-overview.md)文章对此工具进行了说明。

为了尽可能减少网络延迟，请在数据库所在的同一 Azure 地理区域运行测试。

## <a name="step-7-post-implementation-monitoring"></a>步骤 7：实施后的监视

建议监视在生产环境中实施 In-Memory 后的性能影响：

* [监视内存中存储](in-memory-oltp-monitor-space.md)。
* [使用动态管理视图进行监控](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>相关链接

* [内存中 OLTP（内存中优化）](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [本机编译的存储过程简介](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [内存优化顾问](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
