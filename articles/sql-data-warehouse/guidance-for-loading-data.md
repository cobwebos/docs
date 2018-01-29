---
title: "数据加载最佳做法 - Azure SQL 数据仓库 | Microsoft Docs"
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
ms.openlocfilehash: 80974f7660696887783e97b674e2d9921fe2feac
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>将数据加载到 Azure SQL 数据仓库中的最佳做法
关于如何将数据加载到 Azure SQL 数据仓库中的建议以及与之相关的性能优化。 

- 若要详细了解 PolyBase 以及如何设计提取、加载和转换 (ELT) 过程，请参阅[为 SQL 数据仓库设计 ELT](design-elt-data-loading.md)。
- 如需加载教程，请参阅[使用 PolyBase 将数据从 Azure Blob 存储加载到 Azure SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)。


## <a name="preparing-data-in-azure-storage"></a>在 Azure 存储中准备数据
若要尽量减少延迟，请将存储层和数据仓库并置。

将数据导出为 ORC 文件格式时，如果存在较大的文本列，可能会收到“Java 内存不足”错误。 若要解决此限制方面的问题，请仅导出列的一个子集。

PolyBase 无法加载数据大小超过 1,000,000 字节的行。 将数据置于 Azure Blob 存储或 Azure Data Lake Store 的文本文件中时，这些数据必须少于 1,000,000 字节。 无论表架构如何，都有此字节限制。

所有文件格式有不同的性能特征。 为实现最快加载，请使用压缩的分隔文本文件。 UTF-8 和 UTF-16 之间的性能差异是最小的。

将大型压缩文件拆分为较小的压缩文件。

## <a name="running-loads-with-enough-compute"></a>使用足够的计算资源运行负载

若要尽量提高加载速度，请一次只运行一个加载作业。 如果这不可行，请将同时运行的负载的数量降至最低。 如果预期的加载作业较大，可以考虑在加载前纵向扩展数据仓库。

若要使用适当的计算资源运行负载，请创建指定运行负载的加载用户。 将每个加载用户分配给一个特定的资源类。 若要运行负载，请以某个加载用户的身份登录，然后运行该负载。 该负载使用用户的资源类运行。  与尝试根据当前的资源类需求更改用户的资源类相比，此方法更简单。

### <a name="example-of-creating-a-loading-user"></a>创建加载用户的示例
此示例为 staticrc20 资源类创建加载用户。 第一步是**连接到主服务器**并创建登录名。

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```
连接到数据仓库并创建用户。 以下代码假定已连接到名为 mySampleDataWarehouse 的数据库。 它演示如何创建一个名为 LoaderRC20 的用户，并向该用户授予对此数据库的控制权限。 然后将该用户添加为 staticrc20 数据库角色的成员。  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```
若要使用 staticRC20 资源类的资源运行负载，请直接以 LoaderRC20 身份登录，然后运行该负载。

在静态而非动态资源类下运行负载。 使用静态资源类可确保不管[服务级别](performance-tiers.md#service-levels)如何，资源始终不变。 如果使用动态资源类，则资源因服务级别而异。 对于动态类，如果服务级别降低，则意味着可能需要对加载用户使用更大的资源类。

## <a name="allowing-multiple-users-to-load"></a>允许多个用户进行加载

通常需要允许多个用户将数据加载到数据仓库中。 使用 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] 进行加载需要数据库的“控制”权限。  “控制”权限允许对所有架构进行控制性访问。 可能不需要让所有加载用户都具有对所有架构的控制访问权限。 若要限制权限，请使用 DENY CONTROL 语句。

例如，考虑为部门 A 使用数据库架构 schema_A，为部门 B 使用 schema_B；让数据库用户 user_A 和 user_B 分别作为部门 A 和 B 中加载的 PolyBase 用户。 两个用户均已被授予“控制”数据库权限。 架构 A 和架构 B 的创建者现在使用 DENY 锁定其架构：

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

现在 user_A 和 user_B 被锁在其他部门的架构之外。


## <a name="loading-to-a-staging-table"></a>加载到临时表

若要尽量提高将数据移到数据仓库表中的加载速度，请将数据加载到临时表中。  将临时表定义为堆，并将轮循机制用于分发选项。 

可以认为，加载通常是一个两步的过程：首先将数据加载到临时表中，然后将数据插入生产数据仓库表中。 如果生产表使用哈希分发，在在使用哈希分发来定义临时表的情况下，加载和插入的总时间可能会更短。 加载到临时表需要的时间较长，但第二步将行插入到生产表中不会导致数据跨分布区移动。

## <a name="loading-to-a-columnstore-index"></a>加载到列存储索引

列存储索引需要将数据压缩成高质量的行组，因此需要大量的内存。 若要最大程度地提高压缩和索引效率，列存储索引需将最多 1,048,576 行压缩到每个行组中。 存在内存压力时，列存储索引可能无法达到最大压缩率。 这反过来会影响查询性能。 若要进行深入了解，请参阅[列存储内存优化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

- 若要确保加载用户有足够的内存来实现最大压缩率，请使用属于中大型资源类的加载用户。 
- 加载足够的行，以便完全填充新的行组。 在大容量加载期间，数据会以 1,048,576 行为一个完整的行组直接压缩到列存储中。 不到 102,400 行的加载会将行发送到增量存储中以 B 树索引的形式保存。 如果加载的行太少，这些行可能会全部进入增量存储中，不会立即压缩成列存储格式。


## <a name="handling-loading-failures"></a>处理加载失败

使用外部表的加载可能因“查询已中止 -- 从外部源读取时已达最大拒绝阈值”错误而失败。 此消息表示外部数据包含脏记录。 如果数据类型和列数目与外部表的列定义不匹配，或数据不符合指定的外部文件格式，则会将数据记录视为脏记录。 

若要解决脏记录问题，请确保外部表和外部文件格式定义正确，并且外部数据符合这些定义。 如果外部数据记录的子集是脏的，可以通过使用 CREATE EXTERNAL TABLE 中的拒绝选项，选择拒绝这些查询记录。

## <a name="inserting-data-into-a-production-table"></a>将数据插入生产表
可以使用 [INSERT 语句](/sql/t-sql/statements/insert-transact-sql.md)将数据一次性加载到小型表中，甚至可以使用 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 之类的语句定期重新加载某个查找。  但是，单独插入的效率不如执行大容量加载的效率。 

如果一天中有成千上万的单个插入，可将插入成批进行大容量加载。  制定将单个插入追加到某个文件的流程，然后创建另一流程来定期加载该文件。

## <a name="creating-statistics-after-the-load"></a>创建加载后的统计信息

为了改进查询性能，在首次加载数据或者在数据发生重大更改之后，必须针对所有表的所有列创建统计信息。  有关统计信息的详细说明，请参阅[统计信息][统计信息]。 以下示例针对 Customer_Speed 表的五个列创建统计信息。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>轮换存储密钥
好的安全做法是定期更改 Blob 存储的访问密钥。 由于有两个用于 Blob 存储帐户的存储密钥，因此可以转换着使用这两个密钥。

若要轮换 Azure 存储帐户密钥，请执行以下操作：

1. 创建基于辅助存储访问密钥的第二个数据库范围凭据。
2. 创建基于此新凭据的第二个外部数据源。
3. 删除外部表后又创建该表，使之指向新的外部数据源。 

将外部表迁移到新的数据源之后，请执行以下清理任务：

1. 删除第一个外部数据源。
2. 删除基于主存储访问密钥的第一个数据库范围凭据。
3. 登录 Azure 并重新生成主访问密钥，供下次轮换时使用。


## <a name="next-steps"></a>后续步骤
若要监视数据加载，请参阅[使用 DMV 监视工作负荷](sql-data-warehouse-manage-monitor.md)。



