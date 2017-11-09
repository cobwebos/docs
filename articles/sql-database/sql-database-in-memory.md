---
title: "Azure SQL 数据库内存中技术 | Microsoft 文档"
description: "Azure SQL 数据库内存中技术大幅提升了事务和分析工作负荷的性能。"
services: sql-database
documentationCenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: 250ef341-90e5-492f-b075-b4750d237c05
ms.service: sql-database
ms.custom: develop databases
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jodebrui
ms.openlocfilehash: 8930595821cc7662c4ff792b73eb357f1ba29307
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>在 SQL 数据库中使用内存中技术优化性能

使用 Azure SQL 数据库中的内存中技术可在各种工作负荷上实现性能改善：事务工作负荷（联机事务处理 (OLTP)）、分析工作负荷（联机分析处理 (OLAP)）和混合工作负荷（混合事务/分析处理 (HTAP)）。 由于查询和事务处理的效率提升，内存中技术还可降低成本。 用户通常不需要升级数据库的定价层即可实现性能提升。 在某些情况下，即使是降低定价层，使用内存中技术也能实现性能改善。

以下两个示例演示了如何借助内存中 OLTP 大幅改善性能：

- 使用内存中 OLTP，[仲裁商业解决方案能够使其工作负荷增加一倍，同时节省 70% 的 DTU](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)。
    - DTU 表示“数据库吞吐量单位”，包括资源消耗量的测量值。
- 以下视频使用示例工作负荷演示资源消耗方面的重大改进：[In-Memory OLTP in Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB)（“Azure SQL 数据库中的内存中 OLTP”视频）。
    - 有关更多详细信息，请参阅博客文章：[In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)（“Azure SQL 数据库中的内存中 OLTP”博客文章）

可在高级层中的所有数据库（包括高级弹性池中的数据库）内使用内存中技术。

以下视频介绍了使用 Azure SQL 数据库中的内存中技术可带来的潜在性能提升。 请记住，实际带来的性能提升取决于许多因素，包括工作负荷和数据的性质、数据库的访问模式，等等。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Azure SQL 数据库采用以下内存中技术：

- *内存中 OLTP* 可提升吞吐量并降低事务处理的延迟。 可受益于内存中 OLTP 的情况有：高吞吐量事务处理（例如贸易和游戏）、从事件或 IoT 设备引入数据、缓存、数据加载以及临时表和表变量等情况。
- *聚集列存储索引*可减少存储占用（高达 10 倍）并提高报告和分析查询的性能。 将其与数据集市中的事实数据表结合使用，可在数据库中容纳更多数据并提升性能。 此外，将其与操作数据库中的历史数据结合使用，可存档并查询高达 10 倍的额外数据。
- 用于 HTAP 的*非聚集列存储索引*：通过直接查询操作数据库来帮助获取业务的实时见解，无需运行开销不菲的提取、转换和加载 (ETL) 过程并等待填充数据仓库。 非聚集列存储索引允许非常快速地对 OLTP 数据库执行分析查询，同时减少对操作工作负荷的影响。
- 也可以使用内存优化表与列存储的组合。 使用这种组合可以针对相同的数据极快执行事务处理和并发运行分析查询。

列存储索引和内存中 OLTP 分别在 2012 年和 2014 年加入 SQL Server 产品。 Azure SQL 数据库和 SQL Server 共享内存中技术的相同实现。 今后，这些技术的新功能将首先在 Azure SQL 数据库中发布，再加入到下一个版本的 SQL Server。

本主题全面介绍特定于 Azure SQL 数据库的内存中 OLTP 和列存储索引，并提供示例：
- 介绍这些技术对存储和数据大小限制的影响。
- 介绍如何控制使用这些技术的数据库在不同定价层之间的移动。
- 介绍两个示例，演示如何使用 Azure SQL 数据库中的内存中 OLTP 和列存储索引。

有关详细信息，请参阅以下资源。

有关这些技术的深入信息：

- [内存中 OLTP 的概述和使用方案](https://msdn.microsoft.com/library/mt774593.aspx)（包括客户案例研究参考和入门信息）
- [内存中 OLTP 的文档](http://msdn.microsoft.com/library/dn133186.aspx)
- [列存储索引指南](https://msdn.microsoft.com/library/gg492088.aspx)
- 混合事务/分析处理 (HTAP)，也称为[实时运行分析](https://msdn.microsoft.com/library/dn817827.aspx)

有关内存中 OLTP 的快速入门教程：[快速入门 1：通过内存中 OLTP 技术加速 T-SQL 性能](http://msdn.microsoft.com/library/mt694156.aspx)（另一篇帮助用户入门的文章）

深入介绍这些技术的视频：

- [Azure SQL 数据库中的内存中 OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB)（包含性能优势的演示和自行重现这些结果的步骤）
- [In-Memory OLTP Videos: What it is and When/How to use it](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)（内存中 OLTP 相关视频：定义及其适用时间和使用方法）
- [Columnstore Index: In-Memory Analytics Videos from Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)（列存储索引：来自 Ignite 2016 的内存中分析相关视频）

## <a name="storage-and-data-size"></a>存储和数据大小

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>内存中 OLTP 的数据大小和存储上限

内存中 OLTP 包括用于存储用户数据的内存优化表。 这些表必需在内存可容纳的范围内。 由于内存是直接在 SQL 数据库服务中管理的，因此我们提出了用户数据配额的概念。 这种概念称为*内存中 OLTP 存储*。

每个受支持的独立数据库定价层和每个弹性池定价层都包括一定量的内存中 OLTP 存储。 在编写本文时，每 125 个数据库事务单位 (DTU) 或弹性数据库事务单位 (eDTU) 可使用 1 GB 存储。

对于每个受支持的独立数据库和弹性池定价层可用的内存中 OLTP 存储，[SQL 数据库服务层](sql-database-service-tiers.md)一文提供了正式列表。

以下各项计入内存中 OLTP 存储上限：

- 内存优化表中的活动用户数据行和表变量。 请注意，旧行版本不计入上限。
- 内存优化表中的索引。
- ALTER TABLE 操作的运营开销。

如果达到上限，将会出现超出配额错误，且无法再插入或更新数据。 若要解决此错误，可删除数据或提升数据库或池的定价层。

有关监视内存中 OLTP 存储利用率及配置即将达到上限时的警报的详细信息，请参阅[监视内存中存储](sql-database-in-memory-oltp-monitoring.md)。

#### <a name="about-elastic-pools"></a>关于弹性池

使用弹性池时，池中的所有数据库共享内存中 OLTP 存储。 因此一个数据库中的使用量可能对其他数据库造成影响。 对此，有两个缓解方法：

- 为低于池的 eDTU 计数的数据库整体配置最大 eDTU。 此最大值将池中任意数据库中的内存中 OLTP 存储利用率限制为与 eDTU 计数对应的大小。
- 配置大于 0 的最小 eDTU。 此最小值可保证池中的每个数据库都有与配置的最小 eDTU 对应的可用内存中 OLTP 存储量。

### <a name="data-size-and-storage-for-columnstore-indexes"></a>列存储索引的数据大小和存储

列存储索引不需要在内存可容纳的范围内。 因此，索引大小的唯一上限是最大整体数据库大小，此大小在 [SQL 数据库服务层](sql-database-service-tiers.md)一文中有述。

使用聚集列存储索引时，对基础表存储使用列式压缩。 这种压缩可显著减少用户数据的存储占用，意味着数据库中可容纳更多数据。 使用[纵栏表存档压缩](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression)可进一步提高压缩率。 可实现的压缩量取决于数据的性质，但 10 倍压缩并不少见。

例如，如果数据库的最大大小为 1 TB，则使用列存储索引实现 10 倍压缩时，该数据库中可容纳总共 10 TB 的用户数据。

使用非聚集列存储索引时，仍以传统行存储格式存储基础表。 因此节省的存储小于使用聚集列存储索引节省的空间。 但是，如果使用单个列存储索引取代众多传统非聚集索引，则仍可整体减少表的存储占用。

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>在定价层之间移动使用内存中技术的数据库

升级到更高的定价层时（例如，从标准层升级到高级层），绝对不会出现任何不兼容性或其他问题。 可用的功能和资源只会增加。

但是，降级定价层可能会对数据库造成负面影响。 如果数据库包含内存中 OLTP 对象，则从高级层降级到标准或基本层时，影响就尤为明显。 降级后，内存优化表和列存储索引不可用（即使它们保持可见）。 降低弹性池的定价层或将使用内存中技术的数据库移动到标准或基本弹性池时，也应考虑这些问题。

### <a name="in-memory-oltp"></a>内存中 OLTP

*降级到基本/标准层*：标准或基本层中的数据库不支持内存中 OLTP。 此外，不能将包含任何内存中 OLTP 对象的数据库移到标准或基本层。

将数据库降级到标准/基本层之前，请删除所有内存优化表和表类型，以及所有本机编译的 T-SQL 模块。

可通过编程方式了解给定的数据库是否支持内存中 OLTP。 可执行以下 Transact-SQL 查询：

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

如果查询返回 **1**，则此数据库支持内存中 OLTP。


*降级到较低的高级层*：内存优化表中的数据必须在（与数据库的定价层相关或在弹性池中可用的）内存中 OLTP 存储范围内。 如果尝试降低定价层或将数据库移动到可用内存中 OLTP 存储不足的池，操作会失败。

### <a name="columnstore-indexes"></a>列存储索引

*降级到基本或标准层*：只有高级定价层支持列存储索引，标准或基本层则不支持。 将数据库降级到标准或基本层后，列存储索引不可用。 系统会保留列存储索引，但永远不会利用索引。 如果后来又升级回到高级层，列存储索引立即可供再次利用。

如果有**聚集**列存储索引，则降级层后，整个表不可用。 因此，我们建议在将数据库降级到高级层以下之前，先删除所有聚集列存储索引。

*降级到较低的高级层*：如果整个数据库处于目标定价层的最大数据库大小或弹性池中可用存储的范围内，则此降级操作会成功。 列存储索引不会造成特殊影响。


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1.安装内存中 OLTP 示例

在 [Azure 门户](https://portal.azure.com/)中按几下鼠标，即可创建 AdventureWorksLT 示例数据库。 本部分中的步骤说明如何使用内存中 OLTP 对象项目扩充 AdventureWorksLT 数据库，并演示性能优势。

以下资源提供了更简单、更直观的内存中 OLTP 性能演示：

- 版本：[in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- 源代码：[in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>安装步骤

1. 在 [Azure 门户](https://portal.azure.com/)中，在服务器上创建一个高级数据库。 将“源”设置为 AdventureWorksLT 示例数据库。 有关详细说明，请参阅[创建第一个 Azure SQL 数据库](sql-database-get-started-portal.md)。

2. 使用 SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx) 连接到该数据库。

3. 将 内存中 [OLTP Transact-SQL 脚本](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql)复制到剪贴板。 T-SQL 脚本在步骤 1 创建的 AdventureWorksLT 示例数据库中创建所需的内存中对象。

4. 将 T-SQL 脚本粘贴到 SSMS，并执行该脚本。 `MEMORY_OPTIMIZED = ON` 子句 CREATE TABLE 语句至关重要。 例如：


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>错误 40536


如果在运行 T-SQL 脚本时收到错误 40536，请运行以下 T-SQL 脚本来验证数据库是否支持内存中：


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


结果 **0** 表示不支持内存中，结果 **1** 表示支持。 若要诊断问题，请确保数据库位于高级服务层。


#### <a name="about-the-created-memory-optimized-items"></a>关于创建的内存优化项

**表**：此示例包含以下内存优化表：

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


可以通过 SSMS 中的“对象资源管理器”检查内存优化表。 右键单击“表” > “筛选器” > “筛选器设置” > “内存优化”。 值等于 1。


或者可以查询目录视图，例如：


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**本机编译的存储过程**：可以通过目录视图查询来检查 SalesLT.usp_InsertSalesOrder_inmem：


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>运行示例 OLTP 工作负荷

以下两个*存储过程*的唯一差别在于，第一个过程使用内存优化表版本，而第二个过程使用普通磁盘表：

- SalesLT**.**usp_InsertSalesOrder**_inmem**
- SalesLT**.**usp_InsertSalesOrder**_ondisk**


本部分介绍如何使用便利的 **ostress.exe** 实用程序在压力级别执行两个存储过程。 可以比较完成两个压力回合所需的时间。


运行 ostress.exe 时，建议将参数值传递到以下两个存储过程：

- 使用 -n100，运行大量的并发连接。
- 使用 -r500，让每个连接循环数百次。


但是，建议从较小的值（-n10 和 -r50）开始，确保一切运行正常。


### <a name="script-for-ostressexe"></a>Ostress.exe 的脚本


本部分显示 ostress.exe 命令行中内嵌的 T-SQL 脚本。 此脚本使用前面安装的 T-SQL 脚本所创建的项。


以下脚本会在以下内存优化*表*中插入包含 5 个细目的示例销售订单：

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
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


### <a name="install-rml-utilities-and-ostress"></a>安装 RML 实用程序和 ostress


最好规划在 Azure 虚拟机 (VM) 上运行 ostress.exe。 在 AdventureWorksLT 数据库所在的同一 Azure 地理区域中创建 [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/)。 但可以改为在便携式计算机上运行 ostress.exe。


在 VM 或选择的任何主机上，安装重放标记语言 (RML) 实用工具。 这些实用工具包括 ostress.exe。

有关详细信息，请参阅：
- [内存中 OLTP 的示例数据库](http://msdn.microsoft.com/library/mt465764.aspx)中的 ostress.exe 介绍。
- [内存中 OLTP 的示例数据库](http://msdn.microsoft.com/library/mt465764.aspx)。
- [有关安装 ostress.exe 的博客](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)。



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>首先运行 *_inmem* 压力工作负荷


可以使用 *RML 命令提示符*窗口来运行 ostress.exe 命令行。 命令行参数将指示 ostress：

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


ostress.exe 完成时，会在 RML 命令窗口中写入运行持续时间作为输出的最后一行。 例如，一个较短的测试回合持续大约 1.5 分钟：

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>重置，编辑 *_ondisk*，并重新运行


在获得 *_inmem* 运行结果之后，请针对 *_ondisk* 运行执行以下步骤：


1. 在 SSMS 中运行以下命令重置数据库，删除前面运行的命令所插入的所有数据：
```
EXECUTE Demo.usp_DemoReset;
```

2. 编辑 ostress.exe 命令行，将所有的 *_inmem* 替换为 *_ondisk*。

3. 再次重新运行 ostress.exe，并捕获持续时间结果。

4. 再次重置数据库（可靠地删除大量测试数据）。


#### <a name="expected-comparison-results"></a>预期比较结果

在与数据库相同的 Azure 区域的 Azure VM 上运行 ostress，内存中测试已显示此简化工作负荷大约有 **9 倍**的性能改善。

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2.安装内存中分析示例


本部分比较使用列存储索引与传统 b 树索引时的 IO 和统计信息结果。


通常，在对 OLTP 工作负荷进行实时分析时，最好是使用非群集列存储索引。 有关详细信息，请参阅[列存储索引介绍](http://msdn.microsoft.com/library/gg492088.aspx)。



### <a name="prepare-the-columnstore-analytics-test"></a>准备列存储分析测试


1. 使用 Azure 门户基于示例创建全新的 AdventureWorksLT 数据库。
 - 使用相同的名称。
 - 选择任一高级服务层。

2. 将 [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) 复制到剪贴板。
 - T-SQL 脚本在步骤 1 创建的 AdventureWorksLT 示例数据库中创建所需的内存中对象。
 - 该脚本将创建维度表和两个事实表。 每个事实表中填充了 350 万行。
 - 该脚本可能需要 15 分钟才能完成。

3. 将 T-SQL 脚本粘贴到 SSMS，并执行该脚本。 **CREATE INDEX** 语句中的 **COLUMNSTORE** 关键字至关重要，如下所示：<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

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


```
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

- [快速入门 1：通过内存中 OLTP 技术加速 T-SQL 性能](http://msdn.microsoft.com/library/mt694156.aspx)

- [在现有的 Azure SQL 应用程序中使用内存中 OLTP](sql-database-in-memory-oltp-migration.md)

- [监视内存中 OLTP 存储](sql-database-in-memory-oltp-monitoring.md)（适用于内存中 OLTP）


## <a name="additional-resources"></a>其他资源

#### <a name="deeper-information"></a>深入信息

- [了解 Quorum 如何使用 SQL 数据库中的内存中 OLTP 将关键数据库的工作负荷提高一倍，并将 DTU 降低 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)（“Azure SQL 数据库中的内存中 OLTP”博客文章）

- [了解内存中 OLTP](http://msdn.microsoft.com/library/dn133186.aspx)

- [了解列存储索引](https://msdn.microsoft.com/library/gg492088.aspx)

- [了解实时运行分析](http://msdn.microsoft.com/library/dn817827.aspx)

- 请参阅[有关常用工作负荷模式和迁移注意事项](http://msdn.microsoft.com/library/dn673538.aspx)（介绍内存中 OLTP 往往能够在其中提供显著性能改善的工作负荷模式）

#### <a name="application-design"></a>应用程序设计

- [内存中 OLTP（内存中优化）](http://msdn.microsoft.com/library/dn133186.aspx)

- [在现有的 Azure SQL 应用程序中使用内存中 OLTP](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>工具

- [Azure 门户](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
