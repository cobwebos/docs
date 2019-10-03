---
title: Azure SQL 数据库内存中示例 | Microsoft Docs
description: 使用 OLTP 和列存储示例尝试 Azure SQL 数据库内存中技术。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 5f6f4ce4fc77533a4d893472298ef3a20f153136
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567991"
---
# <a name="in-memory-sample"></a>内存中示例

通过 Azure SQL 数据库中的内存中技术，可以改进应用程序性能，并且可能降低数据库成本。 通过使用 Azure SQL 数据库中的内存中技术，可以使用各种工作负荷实现性能改进。

本文介绍两个示例，演示如何使用 Azure SQL 数据库中的内存中 OLTP 和列存储索引。

有关详细信息，请参阅：
- [内存中 OLTP 的概述和使用方案](https://msdn.microsoft.com/library/mt774593.aspx)（包括客户案例研究参考和入门信息）
- [内存中 OLTP 的文档](https://msdn.microsoft.com/library/dn133186.aspx)
- [列存储索引指南](https://msdn.microsoft.com/library/gg492088.aspx)
- 混合事务/分析处理 (HTAP)，也称为[实时运营分析](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1.安装内存中 OLTP 示例

在 [Azure 门户](https://portal.azure.com/)中按几下鼠标，即可创建 AdventureWorksLT 示例数据库。 本部分中的步骤说明如何使用内存中 OLTP 对象项目扩充 AdventureWorksLT 数据库，并演示性能优势。

以下资源提供了更简单、更直观的内存中 OLTP 性能演示：

- 版本： [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- 源代码：[in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>安装步骤

1. 通过 [Azure 门户](https://portal.azure.com/)，在服务器上创建一个高级或业务关键数据库。 将“源”设置为 AdventureWorksLT 示例数据库。 有关详细说明，请参阅[创建第一个 Azure SQL 数据库](sql-database-single-database-get-started.md)。

2. 使用 SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) 连接到该数据库。

3. 将 内存中 [OLTP Transact-SQL 脚本](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql)复制到剪贴板。 T-SQL 脚本在步骤 1 创建的 AdventureWorksLT 示例数据库中创建所需的内存中对象。

4. 将 T-SQL 脚本粘贴到 SSMS，然后执行该脚本。 `MEMORY_OPTIMIZED = ON` 子句 CREATE TABLE 语句至关重要。 例如：


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>错误 40536


如果在运行 T-SQL 脚本时收到错误 40536，请运行以下 T-SQL 脚本来验证数据库是否支持内存中：


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


结果 **0** 表示不支持内存中，结果 **1** 表示支持。 若要诊断问题，请确保数据库位于“高级”服务层级。


#### <a name="about-the-created-memory-optimized-items"></a>关于创建的内存优化项

**表**：此示例包含以下内存优化表：

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


可以通过 SSMS 中的“对象资源管理器”检查内存优化表。 右键单击“表” > “筛选器” > “筛选器设置” > “内存优化”。 值等于 1。


或者可以查询目录视图，例如：


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**本机编译的存储过程**：可以通过目录视图查询来检查 SalesLT.usp_InsertSalesOrder_inmem：


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>运行示例 OLTP 工作负荷

以下两个*存储过程*的唯一差别在于，第一个过程使用内存优化表版本，而第二个过程使用普通磁盘表：

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


本部分介绍如何使用便利的 **ostress.exe** 实用程序在压力级别执行两个存储过程。 可以比较完成两个压力回合所需的时间。


运行 ostress.exe 时，建议将参数值传递到以下两个存储过程：

- 使用 -n100 运行大量的并发连接。
- 使用 -r500，让每个连接循环数百次。


但是，建议从较小的值（-n10 和 -r50）开始，确保一切运行正常。


### <a name="script-for-ostressexe"></a>Ostress.exe 的脚本


本部分显示 ostress.exe 命令行中内嵌的 T-SQL 脚本。 此脚本使用前面安装的 T-SQL 脚本所创建的项。


以下脚本会在以下内存优化*表*中插入包含 5 个细目的示例销售订单：

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


若要创建上述适用于 ostress.exe 的 T-SQL 脚本的 *_ondisk* 版本，请将两处出现的 *_inmem* 子字符串替换为 *_ondisk*。 这种替换将影响表和存储过程的名称。


### <a name="install-rml-utilities-and-ostress"></a>安装 RML 实用程序和 `ostress`


最好规划在 Azure 虚拟机 (VM) 上运行 ostress.exe。 在 AdventureWorksLT 数据库所在的同一 Azure 地理区域中创建 [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/)。 但可以改为在便携式计算机上运行 ostress.exe。


在 VM 或选择的任何主机上，安装重放标记语言 (RML) 实用工具。 这些实用工具包括 ostress.exe。

有关详细信息，请参阅：
- [内存中 OLTP 的示例数据库](https://msdn.microsoft.com/library/mt465764.aspx)中的 ostress.exe 介绍。
- [内存中 OLTP 的示例数据库](https://msdn.microsoft.com/library/mt465764.aspx)。
- [有关安装 ostress.exe 的博客](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)。



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>首先运行 *_inmem* 压力工作负荷


可以使用 *RML 命令提示符*窗口来运行 ostress.exe 命令行。 命令行参数将 `ostress` 定向到：

- 同时运行 100 个连接 (-n100)。
- 每个连接运行 T-SQL 脚本 50 次 (-r50)。


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


若要运行上述 ostress.exe 命令行：


1. 在 SSMS 中运行以下命令重置数据库数据内容，以删除前面运行的命令所插入的所有数据：

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. 将上述 ostress.exe 命令行的文本复制到剪贴板。

3. 将参数 -S -U -P -d 的 `<placeholders>` 替换为正确的实数值。

4. 在 RML Cmd 窗口中运行编辑后的命令行。


#### <a name="result-is-a-duration"></a>结果是一个持续时间


`ostress.exe` 完成时，会在 RML 命令窗口中写入运行持续时间作为输出的最后一行。 例如，一个较短的测试回合持续大约 1.5 分钟：

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>重置，编辑 _ondisk，然后重新运行


在获得 _inmem 运行结果之后，请针对 _ondisk 运行执行以下步骤：


1. 在 SSMS 中运行以下命令重置数据库，删除前面运行的命令所插入的所有数据：
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. 编辑 ostress.exe 命令行，将所有的 _inmem 替换为 _ondisk。

3. 再次重新运行 ostress.exe，并捕获持续时间结果。

4. 再次重置数据库（可靠地删除大量测试数据）。


#### <a name="expected-comparison-results"></a>预期比较结果

在与数据库相同的 Azure 区域的 Azure VM 上运行 `ostress`，内存中测试已显示此简化工作负荷大约有 9 倍的性能改善。

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2.安装内存中分析示例


本部分比较使用列存储索引与传统 b 树索引时的 IO 和统计信息结果。


通常，在对 OLTP 工作负荷进行实时分析时，最好是使用非群集列存储索引。 有关详细信息，请参阅[列存储索引介绍](https://msdn.microsoft.com/library/gg492088.aspx)。



### <a name="prepare-the-columnstore-analytics-test"></a>准备列存储分析测试


1. 使用 Azure 门户基于示例创建全新的 AdventureWorksLT 数据库。
   - 使用相同的名称。
   - 选择任一“高级”服务层级。

2. 将 [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) 复制到剪贴板。
   - T-SQL 脚本在步骤 1 创建的 AdventureWorksLT 示例数据库中创建所需的内存中对象。
   - 该脚本将创建维度表和两个事实表。 每个事实表中填充了 350 万行。
   - 该脚本可能需要 15 分钟才能完成。

3. 将 T-SQL 脚本粘贴到 SSMS，然后执行该脚本。 **CREATE INDEX** 语句中的 **COLUMNSTORE** 关键字至关重要，如下所示：<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. 将 AdventureWorksLT 设置为兼容级别 130：<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    级别 130 并不与内存中功能直接相关。 但级别 130 通常提供比级别 120 更快的查询性能。


#### <a name="key-tables-and-columnstore-indexes"></a>关键表和列存储索引


- dbo.FactResellerSalesXL_CCI 是具有群集列存储索引的表，已在*数据*级别进一步压缩。

- dbo.FactResellerSalesXL_PageCompressed 是具有等效常规群集索引的表，只在*页面*级别压缩。


#### <a name="key-queries-to-compare-the-columnstore-index"></a>用于比较列存储索引的关键查询


[可以运行多种 T-SQL 查询类型](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql)来查看性能改进情况。 在 T-SQL 脚本的步骤 2 中，请注意这一对查询。 这一对查询只是在一行上有所不同：


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


群集列存储索引位于 FactResellerSalesXL\_CCI 表中。

以下 T-SQL 脚本摘录列出了每个表查询的 IO 和 TIME 统计信息。


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

在采用 P2 定价层的数据库中，与传统索引相比，使用聚集列存储索引时，此查询预期可获得约 9 倍的性能提升。 对于 P15，使用列存储索引有望获得约 57 倍的性能提升。



## <a name="next-steps"></a>后续步骤

- [快速入门 1：通过内存中 OLTP 技术加速 T-SQL 性能](https://msdn.microsoft.com/library/mt694156.aspx)

- [在现有的 Azure SQL 应用程序中使用内存中 OLTP](sql-database-in-memory-oltp-migration.md)

- [监视内存中 OLTP 存储](sql-database-in-memory-oltp-monitoring.md)（适用于内存中 OLTP）


## <a name="additional-resources"></a>其他资源

#### <a name="deeper-information"></a>深入信息

- [了解 Quorum 如何使用 SQL 数据库中的内存中 OLTP 将关键数据库的工作负荷提高一倍，并将 DTU 降低 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)（“Azure SQL 数据库中的内存中 OLTP”博客文章）

- [了解内存中 OLTP](https://msdn.microsoft.com/library/dn133186.aspx)

- [了解列存储索引](https://msdn.microsoft.com/library/gg492088.aspx)

- [了解实时运行分析](https://msdn.microsoft.com/library/dn817827.aspx)

- 请参阅[有关常用工作负荷模式和迁移注意事项](https://msdn.microsoft.com/library/dn673538.aspx)（介绍内存中 OLTP 往往能够在其中提供显著性能改善的工作负荷模式）

#### <a name="application-design"></a>应用程序设计

- [内存中 OLTP（内存中优化）](https://msdn.microsoft.com/library/dn133186.aspx)

- [在现有的 Azure SQL 应用程序中使用内存中 OLTP](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>工具

- [Azure 门户](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
