---
title: 利用具体化视图进行性能优化
description: 在使用具体化视图提高查询性能时应了解的建议和注意事项。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: c1cfd3b4c365a04c3d4704f37e4ed4177fa74619
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692980"
---
# <a name="performance-tuning-with-materialized-views"></a>利用具体化视图进行性能优化 
在 Azure SQL 数据仓库中，具体化视图为复杂的分析查询提供一种低维护方法，以实现快速性能，而无需更改任何查询。 本文讨论有关使用具体化视图的一般指南。


## <a name="materialized-views-vs-standard-views"></a>具体化视图与标准视图
Azure SQL 数据仓库支持标准视图和具体化视图。  两者都是用 SELECT 表达式创建的虚拟表，并以逻辑表的形式呈现给查询。  视图封装常见数据计算的复杂性，并向计算更改添加抽象层，因此无需重新编写查询。  

标准视图将在每次使用视图时计算其数据。  磁盘上没有存储任何数据。 用户通常将标准视图用作一种工具，可帮助组织数据库中的逻辑对象和查询。  若要使用标准视图，查询需要直接引用它。 

像表一样，具体化视图预先计算、存储和维护其在 Azure SQL 数据仓库中的数据。  使用具体化视图时，每次都不需要重新计算。  这就是使用具体化视图中的全部或部分数据的查询可以更快地获得更快的性能。  更好的是，查询可以使用具体化视图，而无需直接引用它，因此无需更改应用程序代码。  

标准视图上的大多数要求仍适用于具体化视图。 有关具体化视图语法和其他要求的详细信息，请参阅[CREATE 具体化视图为 SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)。



| 比较                     | 查看                                         | 具体化视图             
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------| 
|查看定义                 | 存储在 Azure 数据仓库中。              | 存储在 Azure 数据仓库中。    
|查看内容                    | 每次使用视图时生成。   | 在创建视图期间，预先处理并存储在 Azure 数据仓库中。 随着向基础表中添加数据而更新。                                             
|数据刷新                    | 始终更新                               | 始终更新                          
|从复杂查询检索视图数据的速度     | 低                                         | 快速  
|额外存储                   | 否                                           | 是                             
|语法                          | 创建视图                                  | 创建具体化视图为 SELECT           
     
## <a name="benefits-of-using-materialized-views"></a>使用具体化视图的优点

设计良好的具体化视图可提供以下优势：

- 减少带有联接和聚合函数的复杂查询的执行时间。 查询越复杂，执行时保存的可能性就越高。 当查询的计算成本较高并且生成的数据集很小时，就会获得最大的好处。  

- Azure SQL 数据仓库中的优化器可以自动使用已部署的具体化视图来改善查询执行计划。  此过程对于提供更快查询性能的用户是透明的，不需要查询直接引用具体化视图。 

- 需要对视图进行低维护。  基表中的所有增量数据更改将以同步方式自动添加到具体化视图中。  此设计允许查询具体化视图返回与直接查询基表相同的数据。 
- 具体化视图中的数据的分布方式与基表不同。  
- 具体化视图中的数据获取与常规表中的数据相同的高可用性和复原优势。  
 
与其他数据仓库提供程序相比，在 Azure SQL 数据仓库中实现的具体化视图还提供以下附加优势： 

- 自动和同步数据刷新，其中包含基表中的数据更改。 不需要用户操作。 
- 广泛的聚合函数支持。 请参阅[CREATE 具体化视图 AS SELECT （transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)。
- 支持查询特定的具体化视图建议。  请参阅[说明（transact-sql）](https://docs.microsoft.com/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest)。

## <a name="common-scenarios"></a>常见方案  

具体化视图通常用于以下情况： 

**需要提高针对大数据的复杂分析查询的性能**

复杂的分析查询通常使用更多的聚合函数和表联接，导致更多的计算密集型操作，如洗牌和查询执行中的联接。  这就是这些查询需要更长时间才能完成的原因，特别是对于大表。  用户可以为从查询的常见计算返回的数据创建具体化视图，因此当查询需要此数据时，无需重新计算，从而降低计算成本，提高查询响应速度。 

**无或最少查询更改需要更快的性能**

数据仓库中的架构和查询更改通常会保持最小，以支持常规 ETL 操作和报告。  如果视图导致的成本可通过查询性能的提升来抵消，则用户可以使用具体化视图进行查询性能优化。 与其他优化选项（如缩放和统计管理）相比，创建和维护具体化视图时，有影响力的生产更改要少得多，并且它的潜在性能提升也更高。

- 创建或维护具体化视图不会影响针对基表运行的查询。
- 查询优化器可以在查询中自动使用已部署的具体化视图，而无需直接查看引用。 此功能减少了在性能优化中进行查询更改的需求。 

**需要不同的数据分发策略来提高查询性能**

Azure 数据仓库是一种分布式大规模并行处理（MPP）系统。   数据仓库表中的数据使用三个[分发策略](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute)（hash、round_robin 或已复制）之一分布在60节点上。  数据分布是在表创建时指定的，并且在删除表之前保持不变。 具体化视图是磁盘上的虚拟表支持哈希和 round_robin 数据分布。  用户可以选择不同于基表的数据分发，但最好是使用大多数视图的查询的性能。  

## <a name="design-guidance"></a>设计指南 

下面是有关使用具体化视图提高查询性能的一般指导：

**工作负载设计**

在开始创建具体化视图之前，必须对工作负荷进行深入了解，方法是查询模式、重要性、频率和生成的数据的大小。  

用户可以为查询优化器推荐的具体化视图运行说明 WITH_RECOMMENDATIONS < SQL_statement >。  由于这些建议是查询特定的，因此，对于同一工作负荷中的其他查询，使单个查询受益的具体化视图可能不是最佳的。  根据工作负荷需要评估这些建议。  理想的具体化视图是那些能够受益于工作负荷性能的视图。  

**了解更快的查询与成本之间的折衷** 

对于每个具体化视图，都有一个数据存储成本和维护视图的成本。  当基表中的数据更改时，具体化视图的大小会增加，并且其物理结构也会改变。  为了避免查询性能下降，数据仓库引擎单独维护每个具体化视图。  当具体化视图的数量和基表的更改增加时，维护工作负荷会更高。   用户应检查所有具体化视图产生的成本是否可以通过查询性能增益进行偏移。  

您可以对数据库中的具体化视图列表运行此查询： 

```sql
SELECT V.name as materialized_view, V.object_id 
FROM sys.views V 
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
``` 

用于减少具体化视图数量的选项： 

- 确定工作负荷中的复杂查询经常使用的常见数据集。  创建具体化视图来存储这些数据集，以便优化器可以在创建执行计划时将它们用作构建基块。  

- 删除使用率较低或不再需要的具体化视图。  禁用的具体化视图不会被维护，但仍会产生存储费用。  

- 合并在相同或相似基表上创建的具体化视图，即使它们的数据不重叠。  合并具体化视图可能会产生比不同视图的大小大的视图，但会降低视图维护成本。  例如：

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

数据仓库优化器可以自动使用已部署的具体化视图来提高查询性能。  此支持透明地应用于不引用使用具体化视图创建时不支持的聚合的视图和查询的查询。  不需要更改任何查询。 您可以检查查询的估计执行计划，以确认是否使用具体化视图。  

**监视具体化视图** 

具体化视图存储在数据仓库中，就像具有聚集列存储索引（CCI）的表一样。  读取具体化视图中的数据包括扫描 CCI 索引段和应用基表中的任何增量更改。 当增量更改数过高时，从具体化视图解析查询的时间可能比直接查询基表更长。  若要避免查询性能下降，最佳做法是运行[DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)来监视视图的 overhead_ratio （total_rows/max （1，base_view_row））。  如果用户的 overhead_ratio 过高，则应重新生成具体化视图。 

**具体化视图和结果集缓存**

在 Azure SQL 数据仓库中引入了这两项功能，这两项功能大致适用于查询性能优化。  结果集缓存用于获取高并发性，并快速响应静态数据的重复查询。  若要使用缓存的结果，缓存请求查询的形式必须与生成缓存的查询匹配。  此外，缓存的结果必须应用于整个查询。  具体化视图允许基表中的数据更改。  具体化视图中的数据可以应用于查询的一部分。  此支持允许相同的实例化视图用于共享某些计算以提高性能的不同查询。

## <a name="example"></a>示例

此示例使用类似 TPCDS 的查询，该查询可查找通过目录（而不是在商店中）支出更多资金的客户，确定首选客户及其所在国家/地区。   此查询涉及到从三个子 SELECT 语句（涉及 SUM （）和 GROUP BY）的联合中选择 TOP 100 记录。 

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

检查查询的估计执行计划。  有18个洗牌和17个联接操作需要更多时间来执行。 现在，让我们为三个子 SELECT 语句中的每个语句创建一个具体化视图。   

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
再次检查原始查询的执行计划。  现在，联接数从17更改为5，并且不再有序播放。  单击计划中的 "筛选操作" 图标，其 "输出" 列表将显示从具体化视图而不是基表中读取的数据。  

 ![Plan_Output_List_with_Materialized_Views](media/performance-tuning-materialized-views/output-list.png)

对于具体化视图，相同的查询运行得更快，无需更改代码。  

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅 [SQL 数据仓库开发概述](sql-data-warehouse-overview-develop.md)。
