---
title: "数据加载指南 - Azure SQL 数据仓库 | Microsoft Docs"
description: "关于如何使用 Azure SQL 数据仓库加载数据和执行 ELT 的建议。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>关于如何将数据加载到 Azure SQL 数据仓库中的指南
关于如何将数据加载到 Azure SQL 数据仓库中的建议以及与之相关的性能优化。 

- 若要详细了解 PolyBase 以及如何设计提取、加载和转换 (ELT) 过程，请参阅[为 SQL 数据仓库设计 ELT](design-elt-data-loading.md)。
- 如需加载教程，请参阅[使用 PolyBase 将数据从 Azure Blob 存储加载到 Azure SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)。


## <a name="extract-source-data"></a>提取源数据

将数据从 SQL Server 或 Azure SQL 数据仓库导出到 ORC 文件格式时，文字较多的列的数量可能会因 Java 内存不足的错误而被限制为 50 列。 若要解决此问题，请仅导出列的一个子集。


## <a name="land-data-to-azure"></a>将数据移至 Azure
PolyBase 无法加载数据大小超过一百万字节的行。 将数据置于 Azure Blob 存储或 Azure Data Lake Store 中的文本文件中时，这些数据必须少于一百万字节。 无论表架构如何定义，都同样如此。

并置存储层和数据仓库以最小化延迟。

## <a name="data-preparation"></a>数据准备工作

所有文件格式有不同的性能特征。 为实现最快加载，请使用压缩的分隔文本文件。 UTF-8 和 UTF-16 之间的性能差异是最小的。

将大型压缩文件拆分为较小的压缩文件。

## <a name="create-designated-loading-users"></a>创建指定的加载用户
若要使用适当的计算资源运行负载，请创建指定运行负载的加载用户。 将每个加载用户分配给一个特定的资源类。 若要运行负载，请以某个加载用户的身份登录，然后运行该负载。 该负载使用用户的资源类运行。  与尝试根据当前的资源类需求更改用户的资源类相比，此方法更简单。

此代码为 staticrc20 资源类创建加载用户。 它为用户提供数据库的控制权限，然后将该用户添加为 staticrc20 数据库角色的成员。 若要使用 staticRC20 资源类的资源运行负载，请直接以 LoaderRC20 身份登录，然后运行该负载。 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

在静态而非动态资源类下运行负载。 使用静态资源类可确保不管[服务级别](performance-tiers.md#service-levels)如何，资源始终不变。 如果使用动态资源类，则资源因服务级别而异。 对于动态类，如果服务级别降低，则意味着可能需要对加载用户使用更大的资源类。

### <a name="example-for-isolating-loading-users"></a>隔离加载用户的示例

通常需要有多个用户可将数据加载到 SQL 数据仓库中。 由于 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] 需要数据库的“控制”权限，因此最终将有多个用户具有所有架构的控制访问权限。 可使用 DENY CONTROL 语句限制这种情况。

例如，考虑为部门 A 使用数据库架构 schema_A，为部门 B 使用 schema_B；让数据库用户 user_A 和 user_B 分别作为部门 A 和 B 中加载的 PolyBase 用户。 两个用户均已被授予“控制”数据库权限。 架构 A 和架构 B 的创建者现在使用 DENY 锁定其架构：

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

现在 user_A 和 user_B 被锁在其他部门的架构之外。


## <a name="load-to-a-staging-table"></a>加载到临时表

为获得最快加载速度，请加载到 round_robin 堆临时表。 这是将数据从 Azure 存储层移至 SQL 数据仓库最有效的方法。

如果要执行大型加载作业，请纵向扩展数据仓库。

一次只运行一个加载作业以优化加载性能。

### <a name="optimize-columnstore-index-loads"></a>优化列存储索引加载

列存储索引需要将数据压缩成高质量的行组，因此需要大量的内存。 若要最大程度地提高压缩和索引效率，列存储索引需将 1,048,576 行压缩到每个行组中。 这是每个行组的最大行数。 存在内存压力时，列存储索引可能无法达到最大压缩率。 这反过来会影响查询性能。 若要进行深入了解，请参阅[列存储内存优化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

- 若要确保加载用户有足够的内存来实现最大压缩率，请使用属于中大型资源类的加载用户。 
- 加载足够的行，以便完全填充新的行组。 在大容量加载期间，每次都会将 1,048,576 行直接加载到列存储中。 如果加载的数据不到 102,400 行，则会将这些行发送到增量存储中，以聚集索引的形式保存行，直至有足够的行进行压缩。 如果加载的行太少，这些行可能会全部进入增量存储中，不会立即压缩成列存储格式。


### <a name="handling-loading-failures"></a>处理加载失败

使用外部表的加载可能因“查询已中止 -- 从外部源读取时已达最大拒绝阈值”错误而失败。 这表示外部数据包含脏记录。 如果实际数据类型/列数目不匹配外部表的列定义，或数据不匹配指定的外部文件格式，则会将数据记录视为脏记录。 

若要解决此问题，请确保外部表和外部文件格式定义正确，并且外部数据符合这些定义。 如果外部数据记录的子集是脏的，可以通过使用 CREATE EXTERNAL TABLE DDL 中的拒绝选项，选择拒绝这些查询记录。



## <a name="insert-data-into-production-table"></a>将数据插入生产表
下面的建议适用于将行插入生产表中。


### <a name="batch-insert-statements"></a>批处理 INSERT 语句
可以根据需要使用 [INSERT 语句](/sql/t-sql/statements/insert-transact-sql.md)（例如 `INSERT INTO MyLookup VALUES (1, 'Type 1')`）将数据一次性加载到小型表中，甚至可以定期重新加载某个查找。  单独插入的效率不如执行大容量加载的效率。 

如果一天中有成千上万的单个插入，可将插入成批进行大容量加载。  制定将单个插入追加到某个文件的流程，然后创建另一流程来定期加载该文件。

### <a name="create-statistics-after-the-load"></a>创建加载后的统计信息

为了改进查询性能，在首次加载数据或者在数据发生重大更改之后，必须针对所有表的所有列创建统计信息。  有关统计信息的详细说明，请参阅[统计信息][统计信息]。 以下示例针对 Customer_Speed 表的五个列创建统计信息。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>轮换存储密钥
好的安全做法是定期更改 Blob 存储的访问密钥。 Blob 存储帐户有两个存储密钥。 这样是为了便于轮换密钥。

若要轮换 Azure 存储帐户密钥，请执行以下操作：

1. 创建基于辅助存储访问密钥的第二个数据库范围凭据。
2. 创建基于此新凭据的第二个外部数据源。
3. 删除外部表后又创建该表，使之指向新的外部数据源。 

将外部表迁移到新的数据源之后，执行以下清理任务：

1. 删除第一个外部数据源。
2. 删除基于主存储访问密钥的第一个数据库范围凭据。
3. 登录 Azure 并重新生成主访问密钥，供下次轮换时使用。


## <a name="next-steps"></a>后续步骤
若要监视加载过程，请参阅[使用 DMV 监视工作负荷](sql-data-warehouse-manage-monitor.md)。



