---
title: Azure SQL 数据仓库发行说明（2018 年 9 月）| Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: f2fe1bc06f4122a1d27b22288784b3db167c300c
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321693"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Azure SQL 数据仓库中的新增功能 2018 年 9 月
Azure SQL 数据仓库持续得到改进。 本文介绍了 2018 年 9 月发行的版本中所引入的新功能和所做的更改。

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>适用于 SQL 数据仓库 Gen2 的较低新入口点
2018 年 4 月，[Microsoft 公布了](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Azure SQL 数据仓库 Gen2，可提供 5 倍的性能、5 倍的计算规模、4 倍的并发，以及无限的存储空间。 如 Gigaom 撰写的[云基准中的数据仓库](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)中所述，SQL 数据仓库 Gen2 的性能比 Amazon Redshif 高 42%。

Gen2 现在采用较低的入口点 DWU500c，使用户能够运行较小的数据仓库或包含所有最新服务改进的开发/测试环境。 新的入口点保留了所有 Gen2 功能，包括[自适应缓存](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)、[极速数据缓冲](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)和对[实时数据仓库](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/)的支持。

## <a name="improved-availability-with-query-restartability"></a>改进了查询可重启性的可用性
查询执行期间，可能发生任意数量的问题，导致查询失败。 网络中断、硬件故障或其他连接断开都可能导致中断。 SQL 数据仓库现支持针对步骤或语句级别 SELECT 查询的查询可重启性。 

通过查询可重启性，因故障导致中断的处于测试的查询将自动重启。 根据步骤数量，查询的形状或执行期间查询暂停的位置，SQL 数据仓库引擎将重启完整查询或从上次完成的查询步骤继续。 从最终用户的角度来看，查询完成。 

## <a name="maintenance-scheduling-preview"></a>维护计划（预览）
Azure SQL 数据仓库维护计划现在处于预览状态。 这一新功能可无缝集成服务运行状况计划内维护通知、资源运行状况检查监视器和 Azure SQL 数据仓库维护计划服务。 维护计划可让你在方便接收新功能、升级和修补程序时计划时间范围。

维护计划利用 Azure Monitor，可让客户确定如何了解即将发生的维护事件以及应触发哪些自动化流来管理停机时间并最大程度地减少对其操作的影响。 通知可添加电子邮件或文本。 

## <a name="wide-row-support-in-polybase"></a>Polybase 中的宽行支持
如果要将数据加载到 SQL 数据仓库中，通用指南是使用 [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase)，它支持并行数据加载。 此版本支持较宽的列（32k 到 1MB）- 可用于加载包含宽行的表格。 宽行支持简化了包含宽行的表格的数据加载过程。

> [!Note]
> 行的总大小不能超过 1 MB。

## <a name="additional-language-support"></a>其他语言支持
此版本引入了对以下 T-SQL 语言元素的支持：

### <a name="stringsplit"></a>STRING_SPLIT
[STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) 函数可使用指定的分隔符拆分字符字符串。 STRING_SPLIT 可用于数据加载方案，其中列可能包含要分析和插入其他表格的多个值。

#### <a name="example"></a>示例
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>COMPRESS/DECOMPRESS 函数
借助 [COMPRESS](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) 函数，可以使用 GZIP 算法，压缩或解压缩字符串输入。

#### <a name="example"></a>示例

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>用于删除视图的 IF EXISTS 子句
在 [DROP VIEW](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) 语句中添加 IF EXISTS 子句简化了从数据仓库删除视图所需的 T-SQL 代码。 IF EXISTS 语法应用于 DROP VIEW 语句时，如果视图已存在，将删除该视图，如果视图不存在，将忽略该语句。

#### <a name="example"></a>示例
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>改善了单一实例插入和 DDL 语句的编译时间 
遵循针对数据插入的传统的提取、转换和加载 (ETL) 模型，通常会导致将单一实例插入 ([INSERT-VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) 运行到数据库的表格中。 此版本通过减少执行此类语句所需的编译时间，提高了单一实例插入操作的性能。 在某些情况下，观察到编译速度提高了 3 倍。 此项改进可减少执行单一实例插入语句所需的时间。 

此项改进还通过减少数据定义语言 (DDL) 语句（包括 CREATE、ALTER 和 DELETE 操作）的查询编译时间，使包含大量对象的数据仓库受益。 

最后，此项改进减少了对宽表（即包含大量列的表格）执行的语句的执行总次数。 此项改进减少了查询编译步骤的时间，从而减少查询的整体执行时间。

## <a name="bug-fixes"></a>Bug 修复

| 标题 | Description |
|:---|:---|
| **修复了有关创建唯一约束分发的统计信息的问题** | 此修补程序解决了运行仅指定表（表已定义约束）的 UPDATE STATISTICS 时遇到的错误。 |
| **修复了有关对外部表编译查询的问题** | 此修补程序解决了影响涉及外部表的查询编译时间的缺陷。|
| **修复了有关包含大型类型的语句的问题** | 解决了使用声明为大型类型之一（nvarchar(max)、varchar(max) 和 varbinary(max)）的参数的准备语句编译的缺陷。 |
| **修复了有关深度嵌套查询的错误** | 当深度嵌套的查询超过系统限制时，提供清楚的错误消息。|
| **修复了语句包含相关子查询和执行时间常量时的编译时错误** |解决了包含特定相关子查询和执行时间常量（例如 GETDATE()）组合的查询的编译时错误。|
| **解决了获取 PDW 对象锁定和 autostats 的并发槽时的超时** |修补程序添加了锁定超时，防止 autostat 请求长时间阻止发起请求。|

## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请了解如何快速[创建 SQL 数据仓库][create a SQL Data Warehouse]。 如果不熟悉 Azure，在遇到新术语时，可以参考 [Azure 术语表][Azure glossary]。 或者，查看一下以下一些其他 SQL 数据仓库资源。  

* [客户成功案例]
* [博客]
* [功能请求]
* [视频]
* [客户顾问团队博客]
* [堆栈溢出论坛]
* [Twitter]


[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客户顾问团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[客户成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[堆栈溢出论坛]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
