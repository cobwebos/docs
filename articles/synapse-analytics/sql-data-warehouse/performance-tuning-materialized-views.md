---
title: 使用具体化视图优化性能
description: 使用具体化视图提高查询性能时应了解的建议和注意事项。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e942130d9acf803665e52498ef6a4976cc9ade7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743172"
---
# <a name="performance-tuning-with-materialized-views"></a>使用具体化视图优化性能

Synapse SQL 池中的具体化视图为复杂的分析查询提供了一种低维护方法，无需任何查询更改即可获得快速性能。 本文提供有关使用具体化视图的一般性指导。

Azure SQL 数据仓库中的具体化视图为复杂的分析查询提供一种低维护的方法来加速查询性能，且无需对查询进行任何更改。 本文提供有关使用具体化视图的一般性指导。

## <a name="materialized-views-vs-standard-views"></a>具体化视图与标准视图

SQL 池支持标准和具体化视图。  两者都是使用 SELECT 表达式创建的虚拟表，作为逻辑表提供给查询。  视图封装了常见数据计算的复杂性，并为计算更改添加了抽象层，因此无需重写查询。  

标准视图在每次使用视图时都会计算其数据。  磁盘上没有存储数据。 用户通常使用标准视图作为帮助组织数据库中的逻辑对象和查询的工具。  若要使用标准视图，查询需要直接引用它。

具体化视图在 SQL 池中预先计算、存储和维护其数据，就像表一样。  每次使用具体化视图时都不需要重新计算。  正因如此，使用具体化视图中所有或一部分数据的查询可以获得更快的性能。  更有利的是，查询可以使用具体化视图而无需直接引用它，因此不需要更改应用程序代码。  

对标准视图的大部分要求仍适用于具体化视图。 有关具体化视图语法和其他要求的详细信息，请参阅[创建重要性视图作为选择](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

| 比较                     | 视图                                         | 具体化视图
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|查看定义                 | 存储在 SQL 池中。              | 存储在 SQL 池中。
|视图内容                    | 每次使用视图时生成。   | 在创建视图期间预处理并存储在 SQL 池中。 随着在基础表中添加数据而不断更新。
|数据刷新                    | 始终更新                               | 始终更新
|从复杂查询检索视图数据的速度     | 较慢                                         | Fast  
|额外的存储                   | 否                                           | 是
|语法                          | CREATE VIEW                                  | CREATE MATERIALIZED VIEW AS SELECT

## <a name="benefits-of-using-materialized-views"></a>使用具体化视图的优势

合理设计的具体化视图可提供以下优势：

- 减少包含 JOIN 和聚合函数的复杂查询的执行时间。 查询越复杂，节省执行时间的可能性就越大。 当查询的计算成本较高并且生成的数据集较小时优势最大。  
- SQL 池中的优化程序可以自动使用已部署的实物视图来改进查询执行计划。  此过程对于用户是透明的，提供更快的查询性能，且不需要查询直接引用具体化视图。
- 对视图的维护需求较低。  基表中的所有增量数据更改将以同步方式自动添加到具体化视图。  借助这种设计，查询具体化视图返回的数据与直接查询基表相同。
- 具体化视图中数据的分布方式可与基表不同。  
- 具体化视图中数据的高可用性和复原能力与常规表中的数据相同。  

在 SQL 池中实现的具体化视图还提供以下附加优势：

与其他数据仓库提供程序相比，在 Azure SQL 数据仓库中实现的具体化视图还提供以下附加优势：

- 使用基表中的数据更改以同步方式自动刷新数据。 不需要任何用户操作。
- 广泛的聚合函数支持。 请参阅 [CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。
- 支持特定于查询的具体化视图建议。  请参阅 [EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="common-scenarios"></a>常见方案  

以下场景往往使用具体化视图：

**需要改善针对大数据的复杂分析查询的性能**

复杂的分析查询往往使用更多的聚合函数和表联接，导致更多的计算密集型操作，例如查询执行中的随机排布和联接。  因此，这些查询需要更长的时间才能完成，尤其是针对大型表执行时。  

用户可以针对从常见查询计算返回的数据创建具体化视图，因此，当查询需要此数据时无需重新计算，从而可以降低计算成本，并加快查询响应速度。

**需要在不进行任何查询更改，或者只需进行最少量的更改的情况下提高性能**

SQL 池中的架构和查询更改通常保持在最小，以支持常规 ETL 操作和报告。  如果视图产生的成本可以通过查询性能的提升来抵消，则用户可以使用具体化视图来优化查询性能。

与其他优化选项（例如缩放和统计信息管理）相比，创建和维护具体化视图带来的变化对生产造成的影响要小得多，并且性能提升的潜力也更高。

- 创建或维护具体化视图不会影响针对基表运行的查询。
- 查询优化器可以自动使用部署的具体化视图，而无需在查询中直接引用视图。 此功能减少了在性能优化中进行查询更改的需求。

**需要使用不同的数据分布策略来加快查询性能**

SQL 池是一个分布式大规模并行处理 （MPP） 系统。   SQL 池表中的数据使用三种[分发策略](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)之一（哈希、round_robin或复制）分布在 60 个节点上。  

数据分布方式在创建表时指定，在删除该表之前会一直保持不变。 具体化视图在支持哈希和 round_robin 数据分布的磁盘上以虚拟表的形式提供。  用户可以选择与基表不同的、但对于大多数使用视图的查询性能最有利的数据分布方式。  

## <a name="design-guidance"></a>设计指南

下面是有关使用具体化视图改善查询性能的一般性指导：

**工作负荷的设计**

在开始创建具体化视图之前，必须对工作负荷的查询模式、重要性、频率和生成数据的大小有一个深入的了解。  

用户可以针对查询优化器建议的具体化视图运行 EXPLAIN WITH_RECOMMENDATIONS <SQL 语句>。  由于这些建议特定于查询，因此，可使单个查询受益的具体化视图对于同一工作负荷中的其他查询而言不一定最佳的。  

评估这些建议时请考虑到工作负荷的需求。  能够改善工作负荷性能的具体化视图才是最理想的。  

**在查询速度与成本之间进行权衡**

每个具体化视图都会产生数据存储成本以及维护该视图的成本。  随着基表中数据的更改，具体化视图的大小将会增大，其物理结构也会发生变化。  为了避免查询性能下降，每个具体化视图由 SQL 池引擎单独维护。  

当具体化视图的数量和基表的更改增多时，维护工作负荷就会增大。   用户应该检查查询性能的提升是否能够抵消所有具体化视图产生的成本。  

可以针对数据库中的具体化视图列表运行此查询：

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

用于减少具体化视图数量的选项：

- 识别工作负荷中复杂查询经常使用的数据集。  创建执行计划时创建具体化视图来存储这些数据集，使优化器能够将其用作构建基块。  

- 删除使用率较低或不再需要的具体化视图。  已禁用的具体化视图不再受到维护，但仍会产生存储成本。  

- 合并针对相同或类似基表创建的具体化视图，即使这些表中的数据不重叠。  合并具体化视图可能会导致合并后的视图大小比各个视图的大小之和更大，但视图维护成本应会降低。  例如：

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**并非所有性能优化都需要更改查询**

SQL 池优化器可以自动使用已部署的实物视图来提高查询性能。  此项支持以透明方式应用到不引用视图的查询，以及使用不支持用于创建具体化视图的聚合的查询。  无需进行任何查询更改。 可以检查查询的估计执行计划，以确认是否使用了具体化视图。  

**监视具体化视图**

具体化视图存储在 SQL 池中，就像具有群集列存储索引 （CCI） 的表一样。  读取具体化视图中的数据涉及到扫描 CCI 索引段，并应用基表中的任何增量更改。 当增量更改数量过大时，从具体化视图求解查询所需的时间可能比直接查询基表更长。  

为了避免查询性能下降，最好运行[DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)来监视视图overhead_ratio（total_rows / 最大（1，base_view_row）。  如果具体化视图的 overhead_ratio（开销比）过高，则用户应重新生成具体化视图。

**具体化视图和结果集缓存**

这两个功能大约在同一时间在 SQL 池中引入，以便查询性能调优。  结果集缓存用于获得高并发性，使针对静态数据的重复查询快速返回响应。  

若要使用缓存的结果，缓存请求查询的形式必须与生成缓存的查询相匹配。  此外，缓存的结果必须应用于整个查询。  

具体化视图允许基表中发生数据更改。  具体化视图中的数据可应用到查询片段。  通过此项支持，共享某些计算资源的不同查询可以使用相同的具体化视图来提高性能。

## <a name="example"></a>示例

此示例使用类似于 TPCDS 的查询，该查询查找通过产品目录购物花费比在商店中的花费更多的客户，并识别优选客户及其所在国家/地区。   此查询涉及到从包含 SUM() 和 GROUP BY 的三个 SELECT 子语句的联合中选择前 100 个记录。

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

检查查询的估计执行计划。  有 18 个随机排布和 17 个联接操作，这些操作的执行时间更长。 现在，让我们针对每个 SELECT 子语句（共三个）创建一个具体化视图。

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

再次检查原始查询的执行计划。  现在，联接数从 17 个更改为 5 个，并且不再有随机排布操作。  单击计划中的“筛选操作”图标，其“输出列表”将显示已从具体化视图而不是基表读取了数据。  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

使用具体化视图时，同一查询的运行速度更快，且无需进行任何代码更改。  

## <a name="next-steps"></a>后续步骤

有关更多开发提示，请参阅[Synapse SQL 池开发概述](sql-data-warehouse-overview-develop.md)。
