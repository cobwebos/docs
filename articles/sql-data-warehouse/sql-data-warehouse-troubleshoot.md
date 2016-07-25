<properties
   pageTitle="排查 Azure SQL 数据仓库问题 | Azure"
   description="排查 Azure SQL 数据仓库问题。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="06/13/2016"
   wacn.date=""/>

# 排查 Azure SQL 数据仓库问题
本主题列出了你在使用 Azure SQL 数据仓库时可能遇到的一些较常见的问题。

## 连接
较常见的连接问题包括：

- 未设置防火墙规则
- 使用不支持的工具/协议

### 防火墙规则
为了确保只有已知的 IP 地址可以访问数据库，Azure SQL 数据库受到服务器和数据库级别的防火墙保护。默认情况下，防火墙是安全的，这意味着，你需要显式启用单个 IP 地址或地址范围才能进行连接。若要配置防火墙的访问权限，请遵循[预配说明][]中的[为客户端 IP 配置服务器防火墙访问权限][]中所述的步骤。

### 使用不支持的工具/协议
SQL 数据仓库建议使用 [Visual Studio 2013 或 2015][] 来查询数据。针对客户端连接，建议使用 [SQL Server Native Client 10/11 (ODBC)][]。SQL Server Management Studio (SSMS) 尚未受到支持。虽然它的部分功能可以运作，但对象资源管理器树无法与 SQL 数据仓库配合使用，而查询功能可以在忽略某些错误消息之后运行。

## 查询性能

进行数据库设计时，通过执行几项简单的操作就能确保从 SQL 数据仓库获得最佳查询性能。若要了解如何执行查询，这篇关于[了解如何监视查询][]的文章是个不错的起点。有时，只要[缩放 SQL 数据仓库][]来提升计算能力，即可加快查询的执行速度。若要在一个位置找到这些优化，请参阅 [SQL 数据仓库最佳实践][]一文。

下面是导致查询性能问题的一些最常见的原因。

### 统计信息

表上的[统计信息][]包含数据库列或列组合中值范围和频率的相关信息。查询引擎使用这些统计信息优化查询执行以及提高查询性能。与 SQL Server 或 SQL 数据库不同的是，SQL 数据仓库不会自动创建或更新统计信息。必须在所有表上手动维护统计信息。若要了解如何管理统计信息以及识别需要统计信息的表，请参阅[管理 SQL 数据仓库中的统计信息][]一文。

### 表设计

对 SQL 数据仓库所做的最重要的选择之一就是[为表选择合适的哈希分发键][]和[表设计][]。在从开始使用到从 SQL 数据仓库获得更快性能的过程中，一定要了解这些文章中的概念。

### 群集列存储段质量

群集列存储段质量对群集列存储表的最佳查询性能很重要。压缩行组中的行数可以测量分段质量。以下查询会识别出列存储索引段运行状况不佳的表，并生成 T-SQL 来重新生成这些表的列存储索引。此查询结果的第一列提供用于重新生成每个索引的 T-SQL。第二列提供基本资源类建议，用于优化压缩。
 
**步骤 1：**对每个 SQL 数据仓库数据库运行此查询，以识别任何欠佳的群集列存储索引。如果未返回任何行，则表示此回归不会影响你，无需采取进一步操作。


    SELECT 
         'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;' AS [T-SQL to Rebuild Index]
        ,CASE WHEN n.nbr_nodes < 3 THEN 'xlargerc' WHEN n.nbr_nodes BETWEEN 4 AND 6 THEN 'largerc' ELSE 'mediumrc' END AS [Resource Class Recommendation]
        ,s.name AS [Schema Name]
        ,t.name AS [Table Name]
        ,AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) AS [Ave Rows in Compressed Row Groups]
    FROM 
        sys.pdw_nodes_column_store_row_groups rg
        JOIN sys.pdw_nodes_tables pt 
            ON rg.object_id = pt.object_id AND rg.pdw_node_id = pt.pdw_node_id AND pt.distribution_id = rg.distribution_id
        JOIN sys.pdw_table_mappings tm 
            ON pt.name = tm.physical_name
        INNER JOIN sys.tables t 
            ON tm.object_id = t.object_id
    INNER JOIN sys.schemas s
        ON t.schema_id = s.schema_id
    CROSS JOIN (SELECT COUNT(*) nbr_nodes  FROM sys.dm_pdw_nodes WHERE type = 'compute') n
    GROUP BY 
        n.nbr_nodes, s.name, t.name
    HAVING 
        AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000 OR
        AVG(CASE WHEN rg.State = 0 THEN rg.Total_rows ELSE NULL END) < 100000
    
    ORDER BY 
        s.name, t.name

 
**步骤 2：**提高用户的资源类，该用户有权将此表上的索引重新生成为上述查询第二列中建议的资源类。


    EXEC sp_addrolemember 'xlargerc', 'LoadUser'

> [AZURE.NOTE]  上述 LoadUser 应为创建来运行 ALTER INDEX 语句的有效用户。无法更改 db\_owner 用户的资源类。有关资源类以及如何创建新用户的详细信息，请参阅以下链接。

 
**步骤 3：**以步骤 2 中用户的身份登录（例如 ¡°LoadUser¡±，该用户现在使用较高的资源类），执行步骤 1 中的查询所生成的 ALTER INDEX 语句。请确保此用户对步骤 1 中的查询所识别出的表拥有 ALTER 权限。
 
**步骤 4：**重新运行步骤 1 中的查询。如果高效地生成了索引，此查询就不应该返回任何行。如果未返回任何行，则表示操作完成。如果你有多个 SQL DW 数据库，则要对每个数据库都重复此过程。如果返回行，请继续执行步骤 5。
 
**步骤 5：**如果重新运行步骤 1 中的查询会返回行，则表示某些表可能有特宽的行，需要大量内存才能以最佳方式生成群集列存储索引。如果是这种情况，请使用 xlargerc 类对这些表重试此过程。若要更改资源类，请使用 xlargerc 重复步骤 2。然后，对仍有欠佳索引的表重复步骤 3。如果你使用的是 DW100-DW300，也使用了 xlargerc，则可以选择让索引保持原样，或暂时增加 DWU 以便为此操作提供更多内存。
 
**最终步骤：**前文中指定的资源类，是生最高质量列存储索引的建议基本资源类。建议你保留此设置，供用户加载你的数据。不过，如果你想撤消步骤 2 中的更改，可以使用以下命令执行此操作。

    EXEC sp_droprolemember 'smallrc', 'LoadUser'

CCI 表负载的基本资源类指导如下：针对 DW100-DW300 使用 xlargerc，针对 DW400-DW600 使用 largerc，且自 DW1000 起全部使用 mediumrc。这项指导对大多数工作负荷都是很好的做法。其目标是为每个索引生成操作提供 400 MB 或更多内存。不过，一种大小无法满足所有需求。优化列存储索引所需的内存视要加载的数据而定，主要受行大小影响。行宽度较小的表需要较少的内存，行宽度较宽则需要较多内存。如果想要进行试验，可以使用步骤 1 中的查询，看看较小的内存分配能否得到最佳的列存储索引。每个行组平均至少要超过 10 万行。50 万以上更好。你看到的最大值会是每个行组 1 百万行。有关如何管理资源类和并发的详细信息，请参阅以下链接。


## 后续步骤
有关如何优化 SQL 数据仓库解决方案的详细信息，请参考 [SQL 数据仓库最佳实践][]一文。

<!--Image references-->

<!--Article references-->
[缩放 SQL 数据仓库]: /documentation/articles/sql-data-warehouse-manage-compute-overview/
[表设计]: /documentation/articles/sql-data-warehouse-develop-table-design/
[为表选择合适的哈希分发键]: /documentation/articles/sql-data-warehouse-develop-hash-distribution-key/
[development overview]: /documentation/articles/sql-data-warehouse-overview-develop/
[了解如何监视查询]: /documentation/articles/sql-data-warehouse-manage-monitor/
[管理 SQL 数据仓库中的统计信息]: /documentation/articles/sql-data-warehouse-develop-statistics/
[预配说明]: /documentation/articles/sql-data-warehouse-get-started-provision/
[为客户端 IP 配置服务器防火墙访问权限]: /documentation/articles/sql-data-warehouse-get-started-provision/#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 或 2015]: /documentation/articles/sql-data-warehouse-get-started-connect/
[SQL 数据仓库最佳实践]: /documentation/articles/sql-data-warehouse-best-practices/
[统计信息]: /documentation/articles/sql-data-warehouse-develop-statistics/

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/zh-cn/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=Mooncake_0718_2016-->