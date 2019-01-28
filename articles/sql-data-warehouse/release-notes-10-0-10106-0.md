---
title: Azure SQL 数据仓库发行说明（2018 年 12 月）| Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 074276e4550b9b2e347e5cd30c597a1d09f6cb2f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440070"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Azure SQL 数据仓库版本 10.0.10106.0 中的新增功能
Azure SQL 数据仓库 (SQL DW) 不断改进。 本文介绍 SQL 数据仓库版本 10.0.10106.0 中引入的新功能和更改。

## <a name="query-restartability---ctas-and-insertselect"></a>查询可重启性 - CTAS 和插入/选择
在极少数情况下（例如，间歇性网络连接问题、节点故障），Azure SQL 数据仓库中执行的查询可能失败。 长时间运行的语句，例如 [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) 和 INSERT-SELECT 操作，更容易出现这种潜在问题。 在此版本中，Azure SQL 数据仓库为 CTAS 和 INSERT-SELECT 语句（加上以前宣布的 SELECT 语句）实现重试逻辑，使系统能够以透明方式处理这些暂时性问题并防止查询失败。 重试次数和处理的暂时性错误列表由系统进行配置。

## <a name="return-order-by-optimization"></a>返回 Order By 优化
在此版本中，SELECT…ORDER BY 的性能已得到大幅提升。  以前，查询执行引擎按每个计算节点将结果排序，再将结果流式传输到控制节点，然后合并结果。 版本增强后，所有计算节点现在会将其结果发送到单个计算节点，再将结果合并，然后通过计算节点将排序的结果返回给用户。  当查询结果集包含大量的行时，此方式可以显著提升性能。

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>PartitionMove 和 BroadcastMove 的数据移动增强
在 Azure SQL 数据仓库 Gen2 中，ShuffleMove 类型的数据移动步骤利用[此性能增强博客文章](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)中所述的即时数据移动技术。  在此版本中，PartitionMove 和 BroadcastMove 类型的数据移动步骤以相同的即时数据移动技术为后盾。  利用此类数据移动步骤的用户查询可以大幅提升性能。  无需更改代码即可利用这些性能优势。

## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请了解如何快速[创建 SQL 数据仓库][create a SQL Data Warehouse]。 如果不熟悉 Azure，学习新术语时，[Azure 词汇表][Azure glossary] 可以提供帮助。 或者，查看一下以下一些其他 SQL 数据仓库资源。  

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
